Guide for a Nginx cluster with OSPF
This can scale to 32 hosts per ospf area 
Aka: Lasses Lastbalangserings Cluster (LLC)

## Network
On the network side, configure a vlan with ospf and hsrp

```
interface Vlan10
 description # Nginx Cluster #
 ip address 172.16.12.2 255.255.255.0
 no ip redirects
 no ip unreachables
 no ip proxy-arp
 standby version 2
 standby 4 ip 172.16.12.1
 standby 4 preempt
 standby 4 track 1 decrement 10
 standby 4 track 2 decrement 10
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 <random 16 char key>
 ip ospf hello-interval 5
```
Do this on you backup router aswell. Change priority if you want to controll the active hsrp

Now we need some ospf config
```
router ospf 1
 log-adjacency-changes
 auto-cost reference-bandwidth 100000
 area 10 authentication message-digest
 area 10 stub
 network 172.16.12.0 0.0.0.255 area 10
 maximum-paths 32
```



## Server
Now spawn up as many servers as you like.
This guide is focusing on CentOS7, but it works on any distro.

### Configure the interface

vim /etc/sysconfig/network
```
NETWORKING=yes
NETWORKING_IPV6=yes
NOZEROCONF=yes
GATEWAY=172.16.12.1
```
vim /etc/sysconfig/network-scripts/ifcfg-eth0
```
DEVICE=eth0
TYPE=Ethernet
BOOTPROTO=static
ONBOOT=yes
IPADDR=172.16.12.10
NETMASK=255.255.255.0
IPV6INIT=yes
IPV6ADDR=2001:DB8:A:63::101/64
IPV6_DEFAULTGW=FE80::1
```

Alywas update ur servers
```
yum -y update
```

Now we need a dummy device for the virtual ip ospf should annouce.
If you want more dummy interfaces, just increment numdummies

```
echo "dummy" > /etc/modules-load.d/dummy.conf
echo "options dummy numdummies=1" > /etc/modprobe.d/dummy.conf
```

vim /etc/sysconfig/network-scripts/ifcfg-dummy0
```
DEVICE=dummy0
TYPE=Ethernet
ONBOOT=yes
IPADDR=172.16.13.129
NETMASK=255.255.255.255
IPV6INIT=yes
IPV6_AUTOCONF=no
IPV6ADDR=2001:DB8:A:c1::1337/128
``` 

## Bird / OSPF
Install bird 1.6

```
yum install -y wget flex bison readline-devel
cd /usr/local/src
wget ftp://bird.network.cz/pub/bird/bird-1.6.0.tar.gz
tar -zxvf bird-1.6.0.tar.gz
cd bird-1.6.0
# MISSING: denne gir bare ipv6
./configure --enable-ipv6
make
make install
```
#### Systemd
Bird dont come with systemd files, so we have to create them manualy

vim /etc/systemd/system/bird.service
```
[Unit]
Description=BIRD Internet Routing Daemon
After=network-online.target

[Service]
Type=forking
ExecStart=/usr/local/sbin/bird
ExecReload=/usr/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

#### vim /etc/systemd/system/bird6.service
```
[Unit]
Description=BIRD Internet Routing Daemon IPv6
After=network-online.target

[Service]
Type=forking
ExecStart=/usr/local/sbin/bird6
ExecReload=/usr/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```
systemctl daemon-reload
systemctl enable bird
systemctl enable bird6
```



## Nginx


















