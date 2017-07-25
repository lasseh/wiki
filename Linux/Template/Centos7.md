
# Interface names
Change back to ethX
```
vim /etc/default/grub

add "net.ifnames=0" to GRUB_CMDLINE_LINUX

grub2-mkconfig -o /boot/grub2/grub.cfg

rename interfaces in /etc/sysconfig/network-scripts/ifcfg-*

reboot
```

# Bonding
/etc/modprobe.d/bonding.conf
```
alias bond0 bonding
```
/etc/sysconfig/network-scripts/ifcfg-bond0
```
DEVICE=bond0
USERCTL=no
BOOTPROTO=none
ONBOOT=yes
IPADDR=10.0.0.1
NETMASK=255.255.255.0
BONDING_OPTS="miimon=100 mode=active-backup primary=eth0"
```

/etc/sysconfig/network-scripts/ifcfg-ethX
```
DEVICE=ethX
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
```

