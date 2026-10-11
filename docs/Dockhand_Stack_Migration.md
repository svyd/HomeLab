# 🐳 Dockhand Stack Migration Guide
*Reusable guide for migrating existing Docker containers to Dockhand-managed stacks. Tested on NAS & Raspberry Pi.*

---

## 📋 Prerequisites
- SSH access to your host
- `dockhand` installed & running with `/var/run/docker.sock` mounted
- Existing `docker-compose.yml` or container config saved locally
- **⚠️ Critical:** Your data/configs **must use bind mounts** (e.g., `/opt/app/data:/config`) so `docker rm` won't delete your files.

---

## 🛠️ Step 1: Create Directory Structure
Adjust `<DOCKHAND_DATA_PATH>` and `<PROJECT>` to match your setup (e.g., `/volume2/docker/dockhand/data/stacks/sevastopol/`).

```bash
# Create folder
sudo mkdir -p <DOCKHAND_DATA_PATH>/<PROJECT>/<STACK_NAME>

# Set open permissions (matches NAS/Dockhand defaults)
sudo chmod 777 <DOCKHAND_DATA_PATH>/<PROJECT>/<STACK_NAME>
```

---

## 📝 Step 2: Create `compose.yaml`
```bash
sudo nano <DOCKHAND_DATA_PATH>/<PROJECT>/<STACK_NAME>/compose.yaml
```
**Paste your config & verify:**
- ✅ `container_name:` EXACTLY matches the currently running container
- ✅ `restart: unless-stopped` is present
- ✅ Healthcheck uses a direct URL (no undefined `${VARIABLE}`)
- ✅ `devices:` kept if GPU/HW acceleration is used
- Save (`Ctrl+O` → `Enter`), Exit (`Ctrl+X`)

Set file permissions:
```bash
sudo chmod 777 <DOCKHAND_DATA_PATH>/<PROJECT>/<STACK_NAME>/compose.yaml
```

---

## 🔄 Step 3: Deploy the Stack
```bash
# 1. Stop & remove old container (safe with bind mounts)
sudo docker stop <CONTAINER_NAME>
sudo docker rm <CONTAINER_NAME>

# 2. Deploy from your new compose file
cd <DOCKHAND_DATA_PATH>/<PROJECT>/<STACK_NAME>
sudo docker compose up -d
```

---

## 🔗 Step 4: Link in Dockhand UI
1. Open Dockhand Web UI → **Stacks**
2. Find `<STACK_NAME>` (may show as `Untracked`)
3. Click `⋮` or **Browse/Link Compose** → select `stacks/<PROJECT>/<STACK_NAME>/compose.yaml`
4. Confirm. If UI doesn't auto-update:
   ```bash
   sudo docker restart dockhand
   ```

---

## ✅ Step 5: Verify & Finalize
```bash
# Check status & restart policy
docker ps | grep <CONTAINER_NAME>
docker inspect <CONTAINER_NAME> --format='{{.HostConfig.RestartPolicy.Name}}'
# Expected: unless-stopped
```
- 🔄 **Reboot test:** Restart your device. Container should auto-start.
- 🌐 **External services:** Update routes/domains in Tailscale/Nginx/Cloudflare admin consoles to match new networks.

---

## ⚠️ Troubleshooting
| Issue | Fix |
|-------|-----|
| `Conflict. The container name is already in use` | Run `sudo docker stop <name> && sudo docker rm <name>` first |
| `Permission denied` in UI | Ensure folder/file is `777` or copy perms from a working stack |
| `Untracked stack` warning | Use Dockhand's **Link/Browse** feature to attach the `compose.yaml` |
| Healthcheck fails | Replace `${VAR}` with direct URL: `http://localhost:PORT/health` |

> 💡 **Note:** `sudo` only grants CLI access to Docker. Containers run isolated and will use `PUID`/`PGID` or internal users. Your host data remains untouched. `docker rm` deletes only the container process, not bind-mounted volumes.

---
*Save this file. Next time, just replace `<...>` placeholders and follow Steps 1–5.* 🚀
