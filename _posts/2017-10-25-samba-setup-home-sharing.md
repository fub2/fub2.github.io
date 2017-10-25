### Linux side

## step 1
```
sudo apt install samba 
vi /etc/samba/smb.conf
```

with content of:

```
[global]
   workgroup = WORKGROUP
   server string = %h server (Samba, Ubuntu)
   dns proxy = no
   log file = /var/log/samba/log.%m
   max log size = 1000
   syslog = 0
   panic action = /usr/share/samba/panic-action %d
   security = user
   encrypt passwords = true
   passdb backend = tdbsam
   obey pam restrictions = yes
   unix password sync = yes
   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .
   pam password change = yes
   map to guest = bad user
   usershare allow guests = no

[homes]
   comment = Home Directories
   browseable = no
   read only = no
   create mask = 0644
   valid users = %S

[netlogon]
;   comment = Network Logon Service
;   path = /home/samba/netlogon
    guest ok = no
    restrict anonymous = 2
;   read only = yes

[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700

# Windows clients look for this share name as a source of downloadable
# printer drivers
[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no
```

restart samba

```
sudo /etc/init.d/samba restart
```

## Important step

```
sudo smbpasswd -a bryanf
```

### Windows side

Win+R

```
\\LinuxServer\username
```

