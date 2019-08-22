## HAProxy Config as ReverseProxy for OCP Master and Routers

### Required Subscription:
Ensure this is subscribed to and Enabled - **rhel-7-server-rpms**

### Sample Config Gist:
	https://gist.github.com/voduytuan/a919c408f61121b6dcc6

### Configuration for Passthrough:
	https://kb.novaordis.com/index.php/HAProxy_SSL_Pass-Through_Configuration

### Logging:
	https://www.percona.com/blog/2014/10/03/haproxy-give-me-some-logs-on-centos-6-5/

### Installation:
	https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/install_haproxy_example1

```
	# vim /etc/firewalld/services/haproxy-http.xml
	<?xml version="1.0" encoding="utf-8"?>
	<service>
	<short>HAProxy-HTTP</short>
	<description>HAProxy load-balancer</description>
	<port protocol="tcp" port="80"/>
	</service>
	# cd /etc/firewalld/services
	# restorecon haproxy-http.xml
	# chmod 640 haproxy-http.xml


	# vim /etc/firewalld/services/haproxy-https.xml
	<?xml version="1.0" encoding="utf-8"?>
	<service>
	<short>HAProxy-HTTPS</short>
	<description>HAProxy load-balancer</description>
	<port protocol="tcp" port="443"/>
	</service>
	# cd /etc/firewalld/services
	# restorecon haproxy-https.xml
	# chmod 640 haproxy-https.xml


	# cat example.com.crt example.com.key > example.com.pem
	# cp example.com.pem /etc/ssl/private/


	# vim /etc/haproxy/haproxy.cfg
	After Defaults section:
	frontend http_web *:80
	    mode http
	    default_backend rgw

	frontend rgw­-https
	  bind <insert vip ipv4>:443 ssl crt /etc/ssl/private/example.com.pem
	  default_backend rgw

	backend rgw
	    balance roundrobin
	    mode http
	    server  rgw1 10.0.0.71:80 check
	    server  rgw2 10.0.0.80:80 check

	# systemctl enable haproxy
	# systemctl start haproxy

```
## Reverse Proxy for OCP Router
```
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
```
## Reverse Proxy for OCP Master
```
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
