```bash
services:
  static-page:
    image: lscr.io/linuxserver/nginx:latest
    container_name: static-page
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=10
      - TZ=Europe/Kyiv
    volumes:
      - /volume2/docker/static-page:/config/www:ro
    networks:
      - nginx-proxy-manager_frontend
networks:
  nginx-proxy-manager_frontend:
    external: true
```
