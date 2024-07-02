# loadbalancerdemo
loadbalancing-HAproxy

---in haproxy.cfg file ----

# Global settings
global
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
    stats timeout 30s
    user haproxy
    group haproxy
    daemon
    tune.ssl.default-dh-param 2048

# Default settings
defaults
    log     global
    mode    http
    option  httplog
    option  dontlognull
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms
    errorfile 400 /etc/haproxy/errors/400.http
    errorfile 403 /etc/haproxy/errors/403.http
    errorfile 408 /etc/haproxy/errors/408.http
    errorfile 500 /etc/haproxy/errors/500.http
    errorfile 502 /etc/haproxy/errors/502.http
    errorfile 503 /etc/haproxy/errors/503.http
    errorfile 504 /etc/haproxy/errors/504.http

# Frontend configuration for HTTPS
frontend https_frontend
    bind *:443 ssl crt /etc/letsencrypt/live/mydomain.com/fullchain.pem crt-key /etc/letsencrypt/live/mydomain.com/privkey.pem
    mode http
    default_backend http_back

# Backend configuration
backend http_back
    balance roundrobin
    mode http
    option httpchk HEAD / HTTP/1.1\r\nHost:localhost
    server web1 backend1-private-ip:80 check
    server web2 backend2-private-ip:80 check
    server web3 backend3-private-ip:80 check
    server web4 backend4-private-ip:80 check


----Check HAProxy Status: ----
.sudo systemctl status haproxy

----Verify HTTPS Connections:---
.curl -I https://mydomain.com


