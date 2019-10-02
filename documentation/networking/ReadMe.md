## General Notes

SDN MTU and Plugin Type - /etc/origin/node/node-config.yaml 

```
/etc/origin/node/node-config.yaml

networkConfig:
  mtu: 1450 
  networkPluginName: "redhat/openshift-ovs-subnet"

Sample from a All-In-One Cluster:

[root@ip-10-xxx-xx-xx ~]# ifconfig -a br0: flags=4098<BROADCAST,MULTICAST> mtu 8951 ether 66:df:87:02:a5:48 txqueuelen 1000 (Ethernet) RX packets 0 bytes 0 (0.0 B) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 0 bytes 0 (0.0 B) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

docker0: flags=4099<UP,BROADCAST,MULTICAST> mtu 1500 inet 172.17.0.1 netmask 255.255.0.0 broadcast 0.0.0.0 ether 02:42:a5:3b:b3:80 txqueuelen 0 (Ethernet) RX packets 0 bytes 0 (0.0 B) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 0 bytes 0 (0.0 B) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 9001 inet 10.xxx.xx.xx netmask 255.255.255.128 broadcast 10.xxx.xx.xxx ether 02:92:8a:f4:ef:f0 txqueuelen 1000 (Ethernet) RX packets 4330287 bytes 1803466402 (1.6 GiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 4883612 bytes 2237208955 (2.0 GiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING> mtu 65536 inet 127.0.0.1 netmask 255.0.0.0 loop txqueuelen 1000 (Local Loopback) RX packets 26874780 bytes 14999172368 (13.9 GiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 26874780 bytes 14999172368 (13.9 GiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

ovs-system: flags=4098<BROADCAST,MULTICAST> mtu 1500 ether 26:4d:18:bc:79:d3 txqueuelen 1000 (Ethernet) RX packets 0 bytes 0 (0.0 B) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 0 bytes 0 (0.0 B) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

tun0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 inet 100.64.0.1 netmask 255.255.248.0 broadcast 100.64.7.255 ether 76:4f:66:ea:be:f7 txqueuelen 1000 (Ethernet) RX packets 7773292 bytes 4900403498 (4.5 GiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 8012873 bytes 5399188076 (5.0 GiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth041697ca: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether d6🇩🇪98:96:87:30 txqueuelen 0 (Ethernet) RX packets 151461 bytes 49754530 (47.4 MiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 196100 bytes 20365160 (19.4 MiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth368b6e1a: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether aa:19:65:fc:f4:23 txqueuelen 0 (Ethernet) RX packets 50099 bytes 3550103 (3.3 MiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 67118 bytes 4656396 (4.4 MiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth4b63c127: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether 0a:33:df:bd:3b:ce txqueuelen 0 (Ethernet) RX packets 1610204 bytes 1108592505 (1.0 GiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 1784447 bytes 1156614176 (1.0 GiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth4e5cb0cd: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether 22:df:fe🇩🇪f3:15 txqueuelen 0 (Ethernet) RX packets 309188 bytes 121849129 (116.2 MiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 398811 bytes 41206755 (39.2 MiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth5bb51c53: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether a2:1d:65:03:aa:b1 txqueuelen 0 (Ethernet) RX packets 1480303 bytes 1041168642 (992.9 MiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 1131777 bytes 611552358 (583.2 MiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth5e629a71: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether fe:d4:c9:87:2e:31 txqueuelen 0 (Ethernet) RX packets 753391 bytes 609963964 (581.7 MiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 595236 bytes 758641491 (723.4 MiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth6434a9c6: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether c6:7b:bf:d7:8b:36 txqueuelen 0 (Ethernet) RX packets 149396 bytes 182658892 (174.1 MiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 153310 bytes 13199091 (12.5 MiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth6785fcd3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether a2:b5:e2:fa:76:a5 txqueuelen 0 (Ethernet) RX packets 2216898 bytes 1296182874 (1.2 GiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 2223796 bytes 1756971255 (1.6 GiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth75b8a90e: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether 52:45:eb:1c:9a:a1 txqueuelen 0 (Ethernet) RX packets 0 bytes 0 (0.0 B) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 0 bytes 0 (0.0 B) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth813688eb: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether b2:39:2b:19:c4:03 txqueuelen 0 (Ethernet) RX packets 887241 bytes 576467430 (549.7 MiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 1234351 bytes 1016717748 (969.6 MiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

veth96d23b9c: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether be:4d:df:54:12:b1 txqueuelen 0 (Ethernet) RX packets 164897 bytes 18981228 (18.1 MiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 230355 bytes 19285338 (18.3 MiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

vethc198c3bf: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether 8a:85:4d:70:f7:c0 txqueuelen 0 (Ethernet) RX packets 0 bytes 0 (0.0 B) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 0 bytes 0 (0.0 B) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

vethcf9cbcc0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 8951 ether c2:0f:a0:72:08:af txqueuelen 0 (Ethernet) RX packets 216 bytes 60397 (58.9 KiB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 184 bytes 160636 (156.8 KiB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

vxlan_sys_4789: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 65000 ether 1a:c6:fe:0a:e5:62 txqueuelen 1000 (Ethernet) RX packets 0 bytes 0 (0.0 B) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 0 bytes 0 (0.0 B) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0

[root@ip-10-xxx-xx-xx ~]#

```
