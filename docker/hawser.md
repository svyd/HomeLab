# Hawser is specifically designed to let one Dockhand instance manage/observe Docker hosts remotely

[Documentation](https://dockhand.pro/manual/#hawser)

## Create Hawser's stack directory

```bash
sudo mkdir -p /opt/hawser-stacks
sudo chown root:root /opt/hawser-stacks
sudo chmod 755 /opt/hawser-stacks
```

## Create the Hawser Compose file

```bash
services:
  hawser:
    image: ghcr.io/finsys/hawser:latest
    container_name: hawser
    restart: unless-stopped

    labels:
      - "dockhand.update=false"

    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/hawser-stacks:/opt/hawser-stacks

    environment:
      STACKS_DIR: /opt/hawser-stacks
      TOKEN: "the-token-from-dockhand"
      AGENT_NAME: p1

    ports:
      - "2376:2376"
```
