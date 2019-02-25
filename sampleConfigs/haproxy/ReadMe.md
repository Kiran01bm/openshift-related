# HAProxy Config as ReverseProxy for OCP Master and Routers

```
# Reverse Proxy for OCP Router
frontend ocprouter-https
  bind  10.20.10.11:443
  mode tcp
  option tcplog
  default_backend ocprouter

backend ocprouter
    balance roundrobin
    mode tcp
    server  ocprouter1 10.10.10.13:443 check
    server  ocprouter2 10.10.10.14:443 check
    server  ocprouter3 10.10.10.15:443 check

# Reverse Proxy for OCP Master
frontend ocpmaster-https
  bind  10.20.10.11:8443
  mode tcp
  option tcplog
  default_backend ocpmaster

backend ocpmaster
    balance roundrobin
    mode tcp
    server  ocpmaster1 10.10.10.16:8443 check
    server  ocpmaster2 10.10.10.17:8443 check
    server  ocpmaster3 10.10.10.18:8443 check
```
