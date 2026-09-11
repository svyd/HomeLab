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

## Update flow

### Option 1 — use the Hawser stack in local Dockhand
This is actually convenient.
In Pi1's Dockhand:
1. Open the hawser stack.
2. Pull/update the image or redeploy the stack using the updated image.
3. Dockhand recreates the Hawser container.
4. Hawser reconnects to the NAS Dockhand.
The important thing is that you update the stack, rather than trying to update Hawser as an individual container.
Depending on the exact Dockhand version/UI you're using, the button may be called something like Pull, Update, Redeploy, or Recreate.

### Option 2 — do it from SSH
Since the Compose file is already managed by Dockhand, you can also go to the stack directory and use Compose:
```bash
cd /opt/dockhand/data/stacks/<hawser-stack-directory>
sudo docker compose pull
sudo docker compose up -d
```
