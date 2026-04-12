# Guide how to update Portainer version on a Raspberry Pi

### [Source](https://docs.portainer.io/start/upgrade/docker#community-edition)

To update to the latest version of Portainer Server, use the following commands to stop then remove the old version. Your other applications/containers will not be removed.
```bash
docker stop portainer
```
```bash
docker rm portainer
```
Now that you have stopped and removed the old version of Portainer, you must ensure that you have the most up to date version of the image locally. You can do this with a `docker pull` command:
```bash
docker pull portainer/portainer-ce:latest
```
Finally, deploy the updated version of Portainer:
```bash
docker run -d -p 8000:8000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /opt/portainer:/data portainer/portainer-ce:latest
```
