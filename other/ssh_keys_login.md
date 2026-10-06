## RPI

To log into your Raspberry Pi (Pi) from a Mac using SSH keys, first generate keys on your Mac (if you haven't already), then copy your public key to the Pi's `~/.ssh/authorized_keys` file using `ssh-copy-id` or manually, and finally, connect via ssh `pi@<your_pi_ip>` from your Mac's terminal to log in password-free. 

### Step 1: Generate SSH Keys on Your Mac (If Needed)

```bash
ssh-keygen -t rsa -b 4096 -C "rpi-patna" -f ~/.ssh/rpi-patna
```
Press Enter to accept default file locations and leave the passphrase empty (or set one for extra security).  
Your public key is usually `~/.ssh/rpi-patna.pub`

### Step 2: Copy Public Key to Raspberry Pi
Using `ssh-copy-id` `(Recommended):

Run

```bash
ssh-copy-id -i ~/.ssh/your-key-name.pub user@hostname
```
(e.g., `ssh-copy-id pi@raspberrypi.local` or `ssh-copy-id pi@192.168.1.100`).
Enter the 'pi' user's password when prompted; it will copy the key and set permissions.

### Step 3: Connect from Your Mac

```bash
ssh -i ~/.ssh/rpi-patna ash@192.168.4.183
```
### Step 4: Create or edit your SSH config file

```bash
nano ~/.ssh/config
```

### Step 5: Add a host entry for your server

```bash
Host rpi-patna
    HostName 192.168.4.183
    User ash
    IdentityFile ~/.ssh/rpi-patna
```

### Step 6: Set correct permissions (important)

```bash
chmod 600 ~/.ssh/config
```

### Step 7: Now login with a short, clean command

```bash
ssh rpi-patna
```

## Ugreen NAS - works only if Enabled Personal folders

### Generate an Ed25519 key

```bash
ssh-keygen -t ed25519 -C "covenant" -f ~/.ssh/covenant
```
### Display the public key

```bash
cat ~/.ssh/id_ed25519.pub
```

### Prepare the administrator’s SSH directory

Connect to the NAS with the administrator password. Do this under the administrator account before running sudo -i.
Create the required directory and file:

```bash
mkdir -p ~/.ssh
touch ~/.ssh/authorized_keys
```
Apply restrictive permissions:
```bash
chmod go-w "$HOME"
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
### Add the public key
```bash
cat >> ~/.ssh/authorized_keys
```
Paste the complete public-key line, press Enter, and then press Ctrl+D to finish.

Reapply the file permission:
```bash
chmod 600 ~/.ssh/authorized_keys
```
