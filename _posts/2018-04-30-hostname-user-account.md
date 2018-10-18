
### Distro with systemd

  Almost all modern Linux distro comes with systemd an init system used in Linux distributions to bootstrap the user space and to manage system processes after booting.
  
  ```
  $ hostnamectl
  
     Static hostname: gfs03
         Icon name: computer-vm
           Chassis: vm
        Machine ID: beb217fbb4324b7d9959f78xxxxxxxxx
           Boot ID: 123a3aa710314175aec7c54yyyyyyyyy
    Virtualization: qemu
  Operating System: Ubuntu 16.04.3 LTS
            Kernel: Linux 4.10.0-40-generic
      Architecture: x86-64
  ```
  
  I am going to change gfs03 hostname to gfs-server-03:
  
```
$ hostnamectl set-hostname 'gfs-server-03'
```

### Traditional way

2 files to modify /etc/hostname (or /etc/sysconfig/network) and /etc/hosts and then reboot.

updates in /etc/hosts are one particular line:
From:
127.0.1.1 old-host-name

To:
127.0.1.1 new-server-name-here

### create user account

```
useradd -m tim -s /bin/bash
useradd -m -d /test test
passwd test
su - test
useradd -m john -G accounts
useradd -G admins,webadmin,developers tecmint

usermod -aG sudo username
```
