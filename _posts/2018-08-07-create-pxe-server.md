
### Prepare software packages

``` 
  sudo apt update
  apt install isc-dhcp-server
  apt install apache2 tftpd-hpa inetutils-inetd

  wget http://releases.ubuntu.com/xenial/ubuntu-16.04.4-server-amd64.iso
  mount -o loop ubuntu-16.04.4-server-amd64.iso /mnt/cdrom/
  cp -fr /mnt/cdrom/install/netboot/* /var/lib/tftpboot/
  mkdir /var/www/html/xenial-install
  cp -fr /mnt/cdrom/* /var/www/html/xenial-install/
```

### Configure environment

```
  vim /etc/dhcp/dhcpd.conf
  vim /etc/default/tftpd-hpa 
  vim /etc/inetd.conf
  chmod +w /var/lib/tftpboot/pxelinux.cfg/default
  vim /var/lib/tftpboot/pxelinux.cfg/default
```
  
* dhcpd.conf
```
ddns-update-style none;
default-lease-time 600;
max-lease-time 7200;
log-facility local7;

# A slightly different configuration for an internal subnet.
subnet 10.207.80.0 netmask 255.255.252.0 {
  range 10.207.82.135 10.207.82.136;
  allow booting;
  allow bootp;
  next-server 10.207.80.159;
  filename "pxelinux.0";
}
option option-128 code 128 = string;
option option-129 code 129 = text;

host vR730 {
  hardware ethernet 52:54:BE:6b:01:12;
  fixed-address 10.207.82.135;
}
```

* tftpd-hpa
```
# /etc/default/tftpd-hpa

TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/var/lib/tftpboot"
TFTP_ADDRESS=":69"
TFTP_OPTIONS="--secure"
RUN_DAEMON="yes"
OPTIONS="-l -s /var/lib/tftpboot"
```

* inetd.conf
```
tftp dgram udp wait root /usr/sbin/in.tftpd/usr/sbin/in.tftpd -s /var/lib/tftpboot
```

* /var/lib/tftpboot/pxelinux.cfg/default

```
# D-I config version 2.0
# search path for the c32 support libraries (libcom32, libutil etc.)
path ubuntu-installer/amd64/boot-screens/
include ubuntu-installer/amd64/boot-screens/menu.cfg
default ubuntu-installer/amd64/boot-screens/vesamenu.c32
prompt 0
timeout 0

[linux]
label linux
kernel ubuntu-installer/amd64/linux
append ks=http://10.207.80.159/ks.cfg vga=normal initrd=ubuntu-installer/amd64/initrd.gz
ramdisk_size=16432 root=/dev/rd/0 rw --
```
  
* Start DHCP/TFTP/WWW services  
```  
  375  systemctl restart tftpd-hpa
  376  systemctl status tftpd-hpa
  
  381  systemctl restart isc-dhcp-server
  382  systemctl status isc-dhcp-server  
```

Alternative commands
```
  461  /etc/init.d/isc-dhcp-server start 
  463  /etc/init.d/tftpd-hpa start  
```

### Debugging command

```
dhcpd -t /etc/dhcp/dhcpd.conf
tcpdump -i ens32 > tcpdump.log
tail -F /var/log/syslog
```
