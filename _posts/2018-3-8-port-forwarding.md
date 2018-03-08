
NAT in iptables. So that, all the packets coming to 192.168.12.87 and port 80 will be forwarded to 192.168.12.77 port 80

```
#!/bin/sh

echo 1 > /proc/sys/net/ipv4/ip_forward

iptables -F
iptables -t nat -F
iptables -X

iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.12.77:80
iptables -t nat -A POSTROUTING -p tcp -d 192.168.12.77 --dport 80 -j SNAT --to-source 192.168.12.87
```

You have to DNAT incoming traffic on port 80, but you will also need to SNAT the traffic back.

Alternative (and best approach IMHO) :

Depending on what your Web Server is (Apache, NGinx) you should consider an HTTP Proxy on your front-end server (192.168.12.87) :

![mod_proxy](http://httpd.apache.org/docs/current/en/mod/mod_proxy.html) (Apache)

![proxy_pass](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass) (NGinx)

## reference
https://www.systutorials.com/816/port-forwarding-using-iptables/
