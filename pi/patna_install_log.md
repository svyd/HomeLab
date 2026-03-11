```bash

hostname -f && date && df -h && vcgencmd measure_temp && sudo smartctl -a /dev/nvme0n1 | grep Temperature

patna
Wed 11 Mar 17:54:40 EET 2026
Filesystem      Size  Used Avail Use% Mounted on
udev            7.9G     0  7.9G   0% /dev
tmpfs           3.2G   15M  3.2G   1% /run
/dev/nvme0n1p2  917G  6.6G  874G   1% /
tmpfs           8.0G     0  8.0G   0% /dev/shm
tmpfs           5.0M   48K  5.0M   1% /run/lock
tmpfs           1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
tmpfs           8.0G     0  8.0G   0% /tmp
/dev/nvme0n1p1  510M   66M  445M  13% /boot/firmware
tmpfs           1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs           1.0M     0  1.0M   0% /run/credentials/serial-getty@ttyAMA10.service
tmpfs           1.6G   32K  1.6G   1% /run/user/1000
temp=54.9'C
Temperature:                        35 Celsius

patna
Mon  9 Mar 17:26:52 EET 2026
Filesystem      Size  Used Avail Use% Mounted on
udev            7.9G     0  7.9G   0% /dev
tmpfs           3.2G   15M  3.2G   1% /run
/dev/nvme0n1p2  917G  6.6G  874G   1% /
tmpfs           8.0G     0  8.0G   0% /dev/shm
tmpfs           5.0M   48K  5.0M   1% /run/lock
tmpfs           1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
tmpfs           8.0G     0  8.0G   0% /tmp
/dev/nvme0n1p1  510M   66M  445M  13% /boot/firmware
tmpfs           1.0M     0  1.0M   0% /run/credentials/serial-getty@ttyAMA10.service
tmpfs           1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs           1.6G   32K  1.6G   1% /run/user/1000
temp=54.3'C
Temperature:                        35 Celsius

Thu 29 Jan 19:25:40 EET 2026
Filesystem      Size  Used Avail Use% Mounted on
udev            7.9G     0  7.9G   0% /dev
tmpfs           3.2G   15M  3.2G   1% /run
/dev/nvme0n1p2  917G  6.3G  874G   1% /
tmpfs           8.0G     0  8.0G   0% /dev/shm
tmpfs           5.0M   48K  5.0M   1% /run/lock
tmpfs           1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
tmpfs           8.0G     0  8.0G   0% /tmp
/dev/nvme0n1p1  510M   66M  445M  13% /boot/firmware
tmpfs           1.0M     0  1.0M   0% /run/credentials/serial-getty@ttyAMA10.service
tmpfs           1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs           1.6G   32K  1.6G   1% /run/user/1000
temp=45.0'C

Wed 28 Jan 19:14:45 EET 2026
Filesystem      Size  Used Avail Use% Mounted on
udev            7.9G     0  7.9G   0% /dev
tmpfs           3.2G   14M  3.2G   1% /run
/dev/nvme0n1p2  917G  4.1G  876G   1% /
tmpfs           8.0G     0  8.0G   0% /dev/shm
tmpfs           5.0M   48K  5.0M   1% /run/lock
tmpfs           1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
tmpfs           8.0G     0  8.0G   0% /tmp
/dev/nvme0n1p1  510M   66M  445M  13% /boot/firmware
tmpfs           1.0M     0  1.0M   0% /run/credentials/serial-getty@ttyAMA10.service
tmpfs           1.0M     0  1.0M   0% /run/credentials/getty@tty1.service
tmpfs           1.6G   32K  1.6G   1% /run/user/1000
temp=43.9'C

ifconfig eth0
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.4.4  netmask 255.255.252.0  broadcast 192.168.7.255
        inet6 fe80::b7c7:8e6e:907b:3049  prefixlen 64  scopeid 0x20<link>
        ether 88:a2:9e:40:c9:97  txqueuelen 1000  (Ethernet)
        RX packets 8734  bytes 1247933 (1.1 MiB)
        RX errors 0  dropped 2145  overruns 0  frame 0
        TX packets 4696  bytes 496781 (485.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 112
```
