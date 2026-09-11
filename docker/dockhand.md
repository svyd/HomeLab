[Documentation](https://dockhand.pro/#quick-start)
[Tips & Tricks](https://mariushosting.com/dockhand-tips-and-tricks-the-new-portainer-slayer/)

```bash
# 1. Create the folder first (good practice)
sudo mkdir -p /opt/dockhand/data

# 2. Run the container with the new path
docker run -d \
  --name dockhand \
  --restart unless-stopped \
  -p 3000:3000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /opt/dockhand/data:/app/data \
  fnsys/dockhand:latest
```

## Update flow

Update Dockhand first:

```bash
docker pull fnsys/dockhand:latest
docker stop dockhand
docker rm dockhand
```

Then recreate it using the same command you originally used, including:

pi

```bash
docker run -d \
  --name dockhand \
  --restart unless-stopped \
  -p 3000:3000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /opt/dockhand/data:/app/data \
  fnsys/dockhand:latest
  ```
  
NAS

```bash
docker run -d \
  --name dockhand \
  --restart unless-stopped \
  -p 3000:3000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /volume2/docker/dockhand/data:/app/data \
  fnsys/dockhand:latest
  ```
