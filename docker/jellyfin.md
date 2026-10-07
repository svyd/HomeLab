> Note: `volume1` and `volume2` are used in my NAS. `volume1` - HDDs, `volume2` - nvme

```bash
version: "3.9"
services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: Jellyfin-HT
    environment:
      TZ: Europe/Kiev
      user: 1000:10
    volumes:
      - /volume2/docker/jellyfinht/config:/config:rw
      - /volume2/docker/jellyfinht/cache:/cache:rw
      - /volume2/docker/jellyfinht/logs:/logs:rw
      - /volume1/media/movies:/movies:rw
      - /volume1/media/mults:/mults:rw
      - /volume1/media/shows:/shows:rw
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128
      - /dev/dri/card0:/dev/dri/card0
    restart: unless-stopped
    network_mode: host
```
