To check your current OS version:
```bash
cat /etc/os-release
```

```bash
sudo apt update && \
sudo apt full-upgrade -y && \
sudo apt autoremove -y && \
sudo apt autoclean && \
sudo reboot
```

### 1. Update the Package List
This command refreshes your local database of available software versions. It does not download or install any new programs yet.
```bash
sudo apt update
```
### 2. Apply the Upgrades
Using full-upgrade is highly recommended over a basic upgrade. It handles dependency changes seamlessly by adding or removing packages as required by the latest system components
```bash
sudo apt full-upgrade
```
### 3. Removes unused dependencies
Over time, old and unneeded software files can cluster in your storage. Run this command to clean up leftovers:
```bash
sudo apt autoremove -y
```
_(Note: The -y flag automatically answers "yes" to the confirmation prompt.)_
### 4. Optional. Cleans obsolete cached packages
```bash
sudo apt autoclean
```
### 5. Reboot the System
```bash
sudo reboot
```
