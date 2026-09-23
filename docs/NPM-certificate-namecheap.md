# Nginx Proxy Manager — Let's Encrypt Wildcard Certificate with Namecheap

This documents how to issue a Let's Encrypt wildcard certificate in Nginx Proxy Manager (NPM) using Namecheap DNS-01 validation.

## 1. Prerequisites

- A domain registered with Namecheap.
- DNS managed by Namecheap.
- Nginx Proxy Manager running in Docker.
- Namecheap API access enabled.
- A static/public WAN IP is not required for DNS-01 validation.

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
> Therefore, each required subdomain level needs its own wildcard SAN.

---

## 2. Enable Namecheap API Access

Log in to Namecheap.

Go to:

**Profile → Tools → API Access**

Enable API access.

Namecheap may require your current public IP address to be added to the API whitelist.

Record:

- Namecheap username
- API key
- Domain name

> Do not put the API key into this documentation or a Git repository.

---

## 3. Open Nginx Proxy Manager

Open the NPM web interface.

Go to:

**SSL Certificates → Add SSL Certificate → Let's Encrypt**

Choose:

```text
Let's Encrypt
```

---

## 4. Configure the Certificate

Set the domain names to the wildcard domains that you want to use.

Example:

```text
*.nas.yourdomain.org
*.pi1.yourdomain.org
*.pi2.yourdomain.org
```

You can put multiple domains/wildcards into the same certificate.

### Important

Do not add:

```text
*.yourdomain.org
```

unless you actually need it.

The wildcard only matches **one DNS level**.

For example:

```text
*.yourdomain.org
```

covers:

```text
ha.yourdomain.org
npm.yourdomain.org
```

but does **not** cover:

```text
ha.nas.yourdomain.org
```

For that, you need:

```text
*.nas.yourdomain.org
```

---

## 5. Configure the DNS Challenge

Enable:

```text
Use a DNS Challenge
```

For the DNS provider select:

```text
Namecheap
```

NPM will ask for the Namecheap API credentials.

Enter:

```text
Namecheap Username: <your username>
Namecheap API Key: <your API key>
```

Use the credentials/API key directly in NPM.

> Do not save the API key in this documentation or in a Git repository.

---

## 6. Request the Certificate

Click:

**Save**

NPM will ask Let's Encrypt to issue the certificate.

The process is approximately:

```text
NPM
 │
 ├── requests certificate from Let's Encrypt
 │
 ├── creates DNS-01 challenge
 │
 ├── updates Namecheap DNS
 │
 ├── Let's Encrypt verifies the DNS record
 │
 └── certificate is issued
```

DNS-01 validation means the domain does **not** need to point to the NAS or be reachable from the Internet during validation.

---

## 7. Verify the Certificate

After successful issuance, the certificate should appear under:

**SSL Certificates**

Example:

```text
*.nas.yourdomain.org
*.pi1.yourdomain.org
*.pi2.yourdomain.org
```

The certificate can then be selected from an NPM Proxy Host under:

**SSL Certificate**

---

## 8. Configure a Proxy Host

For example:

```text
Domain Names:
qb.nas.yourdomain.org

Scheme:
http

Forward Hostname / IP:
192.168.1.100

Forward Port:
9865
```

Under **SSL**, select the previously created wildcard certificate.

Enable:

```text
Force SSL
HTTP/2 Support
```

Initially leave:

```text
HSTS Enabled: OFF
HSTS Subdomains: OFF
```

unless there is a specific reason to enable HSTS.

The connection then looks like:

```text
Browser
   │
   │ HTTPS
   ▼
Nginx Proxy Manager
   │
   │ HTTP
   ▼
Docker service
```

The backend service does not need to support HTTPS itself.

---

## 9. Configure Local DNS

For services that should be accessible only inside the home network, create local DNS records in Pi-hole.

Example:

```text
qb.nas.yourdomain.org → 192.168.1.100
```

The browser then resolves the hostname directly to the NAS.

Traffic stays inside the LAN:

```text
Client
  │
  │ DNS
  ▼
Pi-hole
  │
  │ 192.168.1.100
  ▼
NPM
  │
  ▼
Docker service
```

The browser still sees a valid Let's Encrypt certificate because the certificate is issued for the public DNS name.

---

## 10. Reuse the Certificate

You do **not** need a new Let's Encrypt certificate for every service.

For example, this single certificate:

```text
*.nas.yourdomain.org
*.pi1.yourdomain.org
*.pi2.yourdomain.org
```

can be reused by multiple NPM Proxy Hosts:

```text
ha.nas.yourdomain.org
jellyfin.nas.yourdomain.org
qb.nas.yourdomain.org
dockhand.nas.yourdomain.org

pihole.pi1.yourdomain.org
dockhand.pi1.yourdomain.org

pihole.pi2.yourdomain.org
dockhand.pi2.yourdomain.org
```

As long as the hostname is covered by one of the wildcard SANs.

---

## 11. Certificate Renewal

Let's Encrypt certificates expire after approximately 90 days.

NPM handles renewal automatically.

Because this uses a **DNS-01 challenge**, NPM can renew the certificate through Namecheap without requiring the services to be publicly accessible.

After creating the certificate, normally no manual renewal is required.

---

## Final Setup

Our setup is essentially:

```text
                    Internet
                       │
                  Namecheap DNS
                       │
                 ┌─────┴─────┐
                 │            │
              Public       Local DNS
                DNS          Pi-hole
                 │            │
                 └─────┬──────┘
                       │
                       ▼
              Nginx Proxy Manager
                       │
                Let's Encrypt
                wildcard cert
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Home         Jellyfin    qBittorrent
      Assistant
```

### Certificate

```text
*.nas.yourdomain.org
*.pi1.yourdomain.org
*.pi2.yourdomain.org
```

### Validation

```text
Let's Encrypt
      │
      │ DNS-01
      ▼
Namecheap DNS
```

### Important Principles

1. **One wildcard certificate can be reused by many NPM Proxy Hosts.**
2. **A wildcard only covers one DNS level.**
3. **DNS-01 validation does not require the service to be publicly accessible.**
4. **Pi-hole local DNS can point the hostname directly to the LAN IP of NPM.**
5. **NPM terminates HTTPS; the backend service can continue using HTTP.**
6. **NPM handles Let's Encrypt renewal automatically.**

Create a new certificate only when you need a hostname that is not covered by the existing certificate.
