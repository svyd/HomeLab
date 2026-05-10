- [Unbound](#unbound)
- [Default config](#default-config)
- [Macvlan setup](#macvlan-setup)
- [Lists](#lists)
- [Lists from Arsenii](#lists-from-arsenii)
- [How to test](#how-to-test)

[Pi-hole documentation](https://docs.pi-hole.net/docker/)

# Unbound
```bash
version: "3"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    platform: linux/arm64
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "8080:80/tcp"
      - "8443:443/tcp"
    environment:
      TZ: "Europe/Kyiv"
      FTLCONF_webserver_api_password: password
      FTLCONF_dns_listeningMode: "all"
      FTLCONF_dns_upstreams: unbound # ← Uses Docker service name, port 53 by default
    volumes:
      - "/opt/pihole/etc-pihole:/etc/pihole"
      - "/opt/pihole/dnsmasq:/etc/dnsmasq.d"
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
    depends_on:
      - unbound

  unbound:
    container_name: unbound
    image: klutchell/unbound:latest
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "drill", "@127.0.0.1", "dnssec.works", "A"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

networks:
  default:
    driver: bridge
```

# Default config
Create a new folder /opt/pihole/pihole/etc-pihole

    sudo mkdir /opt/pihole/etc-pihole
```bash
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "8080:80/tcp" # Use 8080:80/tcp if you have another service running on port 80
      - "8443:443/tcp"
    environment:
      TZ: "Europe/Kyiv" # https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
      FTLCONF_webserver_api_password: password
      # If using Docker's default `bridge` network setting the dns listening mode should be set to 'all'
      FTLCONF_dns_listeningMode: "all"
    volumes:
      - "/opt/pihole/etc-pihole:/etc/pihole"
    restart: unless-stopped
```

# Macvlan setup
Remove exposed ports and attach the container to your custom macvlan network.
For DHCP, you must manually assign a static IP inside your subnet — DHCP doesn’t work directly in Docker macvlan mode.

```bash
version: "3.7"

services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    networks:
      pihole_net_secondary:
        ipv4_address: 192.168.4.5  # Pick an unused IP in your LAN
    environment:
      TZ: 'Europe/Kyiv'
      FTLCONF_webserver_api_password: password
      FTLCONF_dns_listeningMode: 'all'
    volumes:
      - '/volume2/docker/pihole-secondary/etc-pihole:/etc/pihole'
    restart: unless-stopped
networks:
  pihole_net_secondary:
    external: true
```

# Lists
Source: https://www.reddit.com/r/pihole/comments/1jhlq0j/why_isnt_my_pihole_blocking_ads/
```bash
https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
https://v.firebog.net/hosts/AdguardDNS.txt
https://osint.digitalside.it/Threat-Intel/lists/latestdomains.txt
https://urlhaus.abuse.ch/downloads/hostfile/
https://blocklistproject.github.io/Lists/abuse.txt
https://blocklistproject.github.io/Lists/fraud.txt
https://blocklistproject.github.io/Lists/malware.txt
https://blocklistproject.github.io/Lists/phishing.txt
https://blocklistproject.github.io/Lists/ransomware.txt
https://blocklistproject.github.io/Lists/scam.txt
https://big.oisd.nl/
https://raw.githubusercontent.com/hagezi/dns-blocklists/main/adblock/pro.txt
```

# Lists from Arsenii

```bash
https://raw.githubusercontent.com/PolishFiltersTeam/KADhosts/master/KADhosts.txt
https://raw.githubusercontent.com/FadeMind/hosts.extras/master/add.Spam/hosts
https://v.firebog.net/hosts/static/w3kbl.txt
https://adaway.org/hosts.txt
https://v.firebog.net/hosts/Admiral.txt
https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt
https://v.firebog.net/hosts/Easylist.txt
https://pgl.yoyo.org/adservers/serverlist.php?hostformat=hosts&showintro=0&mimetype=plaintext
https://raw.githubusercontent.com/FadeMind/hosts.extras/master/UncheckyAds/hosts
https://raw.githubusercontent.com/bigdargon/hostsVN/master/hosts
https://raw.githubusercontent.com/jdlingyu/ad-wars/master/hosts
https://v.firebog.net/hosts/Easyprivacy.txt
https://v.firebog.net/hosts/Prigent-Ads.txt
https://raw.githubusercontent.com/FadeMind/hosts.extras/master/add.2o7Net/hosts
https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt
https://hostfiles.frogeye.fr/firstparty-trackers-hosts.txt
https://www.github.developerdan.com/hosts/lists/ads-and-tracking-extended.txt
```

# How to test

On host with installed pi-hole:

    dig docs.portainer.io @127.0.0.1

Expected output:

```bash
; <<>> DiG 9.20.18-1~deb13u1-Debian <<>> docs.portainer.io @127.0.0.1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 53236
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;docs.portainer.io.		IN	A

;; ANSWER SECTION:
docs.portainer.io.	300	IN	CNAME	hosting.gitbook.io.
hosting.gitbook.io.	300	IN	A	172.64.147.209
hosting.gitbook.io.	300	IN	A	104.18.40.47

;; Query time: 179 msec
;; SERVER: 127.0.0.1#53(127.0.0.1) (UDP)
;; WHEN: Thu Jan 29 18:51:39 EET 2026
;; MSG SIZE  rcvd: 108
```

On client host:

    nslookup docs.portainer.io 192.168.4.4

Expected output

```bash
Server:		192.168.4.4
Address:	192.168.4.4#53

Non-authoritative answer:
docs.portainer.io	canonical name = hosting.gitbook.io.
Name:	hosting.gitbook.io
Address: 172.64.147.209
Name:	hosting.gitbook.io
Address: 104.18.40.47
```
