# Given a CA certificate file foo.crt, follow these steps to install it on Ubuntu:

* Create a directory for extra CA certificates in /usr/share/ca-certificates:
```sudo mkdir /usr/share/ca-certificates/extra```
* Copy the CA .crt file to this directory:
```sudo cp foo.crt /usr/share/ca-certificates/extra/foo.crt```
* Let Ubuntu add the .crt file's path relative to /usr/share/ca-certificates to /etc/ca-certificates.conf:
```sudo dpkg-reconfigure ca-certificates```
* In case of a .pem file on Ubuntu, it must first be converted to a .crt file:
```openssl x509 -in foo.pem -inform PEM -out foo.crt```
