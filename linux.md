# Linux

## Kernel

```bash
cpio -id < <initramfs>
```


## Systemd

```bash
systemd-analyze
systemd-analyze blame
systemd-analyze critical-chain
systemd-analyze plot > boot.svg


systemctl isolate <multi-user.target>
systemctl get-default
systemctl set-default <multi-user.target>
runlevel
systemctl reboot


systemctl enable/disable/start/stop/mask/unmask
systemctl status
systemctl list-unit-files
systemctl list-units
systemctl list-units --type service --state running
systemctl edit <service>
systemctl edit --full
systemctl --failed --all
systemctl set-property
(ip accounting, ipaddressallow.../devicepolicy, deviceallow)

systemctl list-dependencies
systemctl list-dependencies --reverse

timedatectl
(timedatectl set-ntp yes)
localectl
loginctl
hostnamectl
networkctl
keyctl
wdctl

systemd-cgls
systemd-cgtop

### journalctl

    -k (dmesg)
    -b < boot_number > (How many reboots ago 0, -1, -2, etc.)
    -o short-precise (dmesg -T)
    -p priority Filter by priority output (4 to filter out notice and info).
All boot cycles : journalctl -o short-precise -k -b all
Current boot : journalctl -o short-precise -k
Last boot : journalctl -o short-precise -k -b -1
Two boots prior : journalctl -o short-precise -k -b -2

journalctl -u <unit>
journalctl -b # log begin from boot
journalctl -e # log end
journalctl -f
write log to disk (binary format): mkdir -p /var/log/journal
```


## Recovery

### MBR

4 partition limit
32bit addr. limit (2TB)

extended logical partiton use links, if a link broken lose all...

Sentinel 2bytes '0xAA55' bootable

backup: dd if=/dev/sda of=/rot/sda.mbr count=1 bs=512
restore: dd of=/rot/sda.mbr if=/dev/sda


### Partitioning

list: `lsblk`, `blkid`
ruin mbr: `dd if=/dev/zero of=/dev/sdb count=1 bs=512`

```bash
fdisk /dev/sdb

parted /dev/sdb mklabel msdos
parted /dev/sdb print

gdisk /dev/sdb
```

### GUID Partition tabel - GPT
need kernel support
64bit addr. (2ZB)
CRC32 chksum


### Grub

```bash
grub install /dev/sdb
grub-install hd0
grub-install --recheck hd0

mount -o remount,ro /
```


#### Grub2
```bash
grub2-mkconfig -o
grub2-script-check -v <config>
/etc/grub.d/
```


## Other

```bash
sysctl

ulimit
ulimit -a
ulimit -Hu
ulimit -Su
ulimit -u 3000
/etc/security/limits.conf

chroot
    ldd /bin/bash

ss -ntl
ss -l '( sport = :ssh )'
```


## Software Management

```bash
dpkg --get-selections
dpkg --get-selections | grep deinstall
dpkg --get-selections | grep -w install
dpkg --purge $(dpkg --get-selections | grep deinstall | cut -f1)


dpkg-reconfigure debconf # set to ask for everything
dpkg-reconfigure tzdata
add-apt-repository <<ppa>

DEBIAN_PRIORITY=low


dpkg -l vim
dpkg -L vim
dpkg -S /usr/bin/vim.basic

dpkg -i prog.deb
/var/cache/apt/archives
apt clean

apt show nginx
apt search apache
search in name: aptitude search ~nApache
search in description: aptitude search ~dApache

dpkg-architecture -L


chown -R root.root packages/
dpkg-deb  --build packages/ <targetdebname.deb>
```


## File access control

```bash
umask
stat <fie/dir>

sticky bit (/tmp,...)
chmod o+t
chmod 1777

sgid bit (apache docroot)
chgrp apache /var/www/html
chmode g+s ...

suid bit (/usr/bin/passwd)

mkdir -m 770
```

### ACL

```bash
getfacl
setfacl
```


## Apparmor

```bash
capsh --print
apparmor_status
aa-autodep ping
/etc/apparmor.d/
```


## Managing file server

```bash
/etc/network/interfaces
/etc/samba/smb.conf
/var/log/samba/log.nmbd

net rpc services list -U root

dpkg-reconfigure openssh-server
ssh-keygen -R <ip>

apt install samba winbind?

ss -ntl
ss -tl
grep -Ew '(139|445)/tcp' /etc/services
smbdr/nmbd/winbind -.service

pdbedit --create root
prinft "%s\n%s\n" pass1 pass1 | sudo pdbedit -a -t <username>
pdbedit --list
    -w -v -u

pdbedit -P "masimum password age"
pdbedit -P ""

nmblookup samba
smbstatus -V
smbstatus -b

grep -FB2 'passdb backend' smb.conf

testparm -s
testparm -sv --show-al-parameters

net share
smbclient -L
```
