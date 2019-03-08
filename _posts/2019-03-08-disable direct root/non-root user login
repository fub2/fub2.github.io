
How do I secure SSH to disable direct root/non-root user login?
===============================================================

Answer :
The procedure described here disallows direct root login, so when you connect using SSH you need to first login as a normal user, then su to obtain root access.

* Disabling root login
1. Edit the /etc/ssh/sshd_config file with a text editor and find the following line:

`#PermitRootLogin yes`

2. Change the yes to no and remove the ‘#’ at the beginning of the line so that it reads :
 
`PermitRootLogin no`

3. Restart the sshd service:

`# service sshd restart`

* Disabling direct root login for non-root users

Sometimes allowing all users the ability to remotely log onto a system can be a security risk. There are many ways to limit who can remotely access a system. You can use PAM, IPwrapers, or IPtables to name a few. However, one of the easiest ways to limit who can access a system via SSH is to configure the SSH daemon.
The directive, AllowUsers, can be configured in /etc/ssh/sshd_config. This directive can be followed by the list of user name patterns, separated by spaces. If specified, login is allowed only for those user names.

`AllowUsers [username]`

Where [username] is the username you want to allow.

For example, to allow ssh login to users john and teena and disable for it for rest of the users, modify the AllowUsers directive as :

`AllowUsers john teena`

Restart the sshd service for the changes to take effect :

`# service sshd restart`

*Allow / disallow groups ssh login

To restrict groups, the option AllowGroups and DenyGroups are used in the file /etc/ssh/sshd_config. The said options will allow or disallow users whose primary group or supplementary group matches one of the group patterns.
