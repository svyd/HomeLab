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
### 3. Clean Up Unused Files
Over time, old and unneeded software files can cluster in your storage. Run this command to clean up leftovers:
```bash
sudo apt autoremove -y
```
_(Note: The -y flag automatically answers "yes" to the confirmation prompt.)_
### 4. Reboot the System
```bash
sudo reboot
```
