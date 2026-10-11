> Note: `volume1` and `volume2` are used in my NAS. `volume1` - HDDs, `volume2` - nvme

```bash
services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    environment:
      - TZ=Europe/Kyiv
      - PUID=1000
      - PGID=10
    labels:
      org.opencontainers.image.source: https://github.com/jellyfin/jellyfin
      org.opencontainers.image.version: 10.9.9
    volumes:
      - /volume2/docker/jellyfin/config:/config:rw
      - /volume2/docker/jellyfin/cache:/cache:rw
      - /volume2/docker/jellyfin/logs:/logs:rw
      - /volume1/media/anime:/anime:ro
      - /volume1/media/movies:/movies:ro
      - /volume1/media/mults:/mults:ro
      - /volume1/media/shows:/shows:ro
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128
      - /dev/dri/card0:/dev/dri/card0
    restart: unless-stopped
    network_mode: host
    healthcheck:
      test:
        - CMD-SHELL
        - curl -Lk -fsS "${HEALTHCHECK_URL}" || exit 1
      interval: 30s
      timeout: 30s
      retries: 3
      start_period: 10s
```
