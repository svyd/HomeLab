```bash
version: '3.9'
services:
  homeassistant:
    container_name: homeassistant
    image: "ghcr.io/home-assistant/home-assistant:stable"
    volumes:
      - /volume2/docker/ha:/config
    restart: unless-stopped
    privileged: false # Залиште true ТІЛЬКИ якщо підключаєте USB Zigbee/Bluetooth до NAS
    network_mode: host
    environment:
      - TZ=Europe/Kyiv
```
## Integrations
* HACS
  * Govee Cloud Integration
  * Xiaomi Home
* MELCloud Home
* Roborock
* Sonos
* Tuya
