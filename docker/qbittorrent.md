[Documentation](https://hub.docker.com/r/linuxserver/qbittorrent)

```bash
services:
  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    environment:
      - PUID=1000
      - PGID=10
      - TZ=Europe/Kyiv
      - WEBUI_PORT=9865
      - TORRENTING_PORT=6881
    volumes:
      - /volume2/docker/qbittorrent/config:/config
      - /volume1/media:/media #optional
    ports:
      - 9865:9865
      - 6881:6881
      - 6881:6881/udp
    stop_grace_period: "10s" #optional
    restart: unless-stopped

```
