# Nginx Proxy Manager — Let's Encrypt Wildcard Certificate with Cloudflare

This documents how to issue a Let's Encrypt wildcard certificate in Nginx Proxy Manager (NPM) using Cloudflare DNS-01 validation.

## 1. Prerequisites

- A domain registered with Cloudflare or using Cloudflare DNS.
- Nginx Proxy Manager running in Docker.
- A Cloudflare API Token with restricted permissions.
- The domain's DNS is managed by Cloudflare.

Example domain:

```text
yourdomain.org
```

Example wildcard domains:

```text
*.nas.yourdomain.org
*.pi1.yourdomain.org
*.pi2.yourdomain.org
```

> A certificate for `*.yourdomain.org` does **not** cover `service.nas.yourdomain.org`.
>
> Each required subdomain level needs its own wildcard SAN.

---

## 2. Create a Cloudflare API Token

Log in to Cloudflare.

Go to:

**Profile → API Tokens → Create Token**

Choose:

**Create Custom Token**

Configure the token with:

### Permissions

```text
Zone → DNS → Edit
Zone → Zone → Read
```

### Zone Resources

Restrict the token to the required zone:

```text
Include → Specific zone → yourdomain.org
```

The final permissions should effectively be:

```text
User API Token
│
├── Zone → DNS → Edit
└── Zone → Zone → Read

Zone Resources
└── yourdomain.org
```

Create the token and save it securely.

> Cloudflare only displays the token after creation. Do not store it in this documentation or commit it to a Git repository.

---

## 3. Open Nginx Proxy Manager

Open the NPM web interface.

Go to:

**SSL Certificates → Add SSL Certificate**

Select:

```text
Let's Encrypt
```

---

## 4. Choose DNS Challenge

Select:

```text
Let's Encrypt via DNS
```

Do **not** use HTTP validation.

Wildcard certificates require DNS-01 validation.

Select:

```text
DNS Provider:
Cloudflare
```

---

## 5. Configure the Certificate

Set the domain names to the wildcard domains that you want to use.

For this setup:

```text
*.nas.yourdomain.org
*.pi1.yourdomain.org
*.pi2.yourdomain.org
```

You can put multiple wildcard domains into the same certificate.

### Important

A wildcard only matches **one DNS level**.

For example:

```text
*.yourdomain.org
```

covers:

```text
ha.yourdomain.org
npm.yourdomain.org
jellyfin.yourdomain.org
```

but does **not** cover:

```text
ha.nas.yourdomain.org
```

For that hostname you need:

```text
*.nas.yourdomain.org
```

---

## 6. Select the Certificate Key Type

Select:

```text
Key Type:
ECDSA

Key Size:
256
```

ECDSA 256 is a good choice for a modern home network because it provides strong security with smaller certificates and efficient TLS handshakes.

RSA 2048 is mainly useful when compatibility with older clients or devices is required.

---

## 7. Configure Cloudflare DNS Credentials

Enter the Cloudflare API credentials requested by NPM.

Use the API Token created in Step 2.

The token should have access only to:

```text
yourdomain.org
```

and only the permissions required for DNS-01 validation:

```text
Zone → DNS → Edit
Zone → Zone → Read
```

> Never paste the API token into chat, documentation, screenshots, or Git repositories.

---

## 8. Request the Certificate

Click:

**Save**

NPM will request the certificate from Let's Encrypt.

The process is approximately:

```text
NPM
 │
 ├── requests certificate from Let's Encrypt
 │
 ├── creates DNS-01 challenge
 │
 ├── updates Cloudflare DNS
 │
 ├── Let's Encrypt verifies the DNS record
 │
 └── certificate is issued
```

DNS-01 validation means the services do not need to be publicly accessible during certificate issuance.

---

## 9. Verify the Certificate

After successful issuance, the certificate should appear under:

**SSL Certificates**

Example:

```text
*.nas.yourdomain.org
*.pi1.yourdomain.org
*.pi2.yourdomain.org
```

The certificate is issued by **Let's Encrypt**.

Cloudflare is only being used as the DNS provider for the DNS-01 challenge.

```text
Certificate issuer:
Let's Encrypt

DNS provider:
Cloudflare
```

---

## 10. Update Existing NPM Proxy Hosts

If an existing Proxy Host uses the old certificate, update it to use the new Cloudflare-DNS-based certificate.

For each Proxy Host:

1. Open **Proxy Hosts**
2. Edit the Proxy Host
3. Open the **SSL** tab
4. Change **SSL Certificate** to the new certificate
5. Keep **Force SSL** enabled
6. Keep **HTTP/2 Support** enabled
7. Keep the existing HSTS configuration
8. Save

Do not change the:

- Domain name
- Forward hostname/IP
- Forward port
- Scheme
- WebSocket setting
- Other proxy configuration

unless there is a separate reason to change them.

---

## 11. Verify All Services

After updating the Proxy Hosts, verify each service using HTTPS.

For example:

```text
https://jellyfin.yourdomain.org
https://ha.nas.yourdomain.org
https://qb.nas.yourdomain.org
https://dockhand.nas.yourdomain.org
```

Also verify services on the Raspberry Pis:

```text
https://pihole.pi1.yourdomain.org
https://dockhand.pi1.yourdomain.org

https://pihole.pi2.yourdomain.org
https://dockhand.pi2.yourdomain.org
```

Check that the browser reports a valid HTTPS certificate and that the certificate covers the requested hostname.

---

## 12. Remove the Old Certificate

Do **not** delete the old certificate immediately.

First:

1. Move every Proxy Host to the new certificate.
2. Test all services.
3. Confirm that no Proxy Host still uses the old certificate.

Only then remove the old certificate from:

**SSL Certificates**

---

## 13. Certificate Renewal

Let's Encrypt certificates expire after approximately 90 days.

NPM handles renewal automatically.

Because the certificate uses a **Cloudflare DNS-01 challenge**, NPM can renew it by using the Cloudflare API Token to create the required DNS challenge records.

No inbound port forwarding is required for certificate renewal.

---

# Final Setup

The certificate workflow is:

```text
                  Cloudflare
                      │
                 DNS API Token
                      │
                      ▼
                    NPM
                      │
               DNS-01 challenge
                      │
                      ▼
                 Let's Encrypt
                      │
                      ▼
              Wildcard Certificate
```

Certificate:

```text
*.nas.yourdomain.org
*.pi1.yourdomain.org
*.pi2.yourdomain.org
```

The certificate can then be reused by multiple NPM Proxy Hosts.

---

# Important Principles

1. **The certificate is issued by Let's Encrypt, not Cloudflare.**
2. **Cloudflare is used as the DNS provider for DNS-01 validation.**
3. **Wildcard certificates require DNS-01 validation.**
4. **One wildcard certificate can be reused by many NPM Proxy Hosts.**
5. **A wildcard only covers one DNS level.**
6. **ECDSA 256 is preferred for modern clients.**
7. **The Cloudflare API Token should be restricted to the required zone.**
8. **Do not expose the API Token or store it in Git.**
9. **NPM automatically renews the certificate.**
10. **No public HTTP/HTTPS port is required for DNS-01 validation.**
11. **Existing Proxy Hosts must be switched to the new certificate manually.**
12. **Keep the old certificate until every Proxy Host has been migrated and tested.**
