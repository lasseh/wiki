Display existing IPv6 Addresses:  
`ifconfig eth0 | grep "inet6 addr:"`  
  
Add an IPv6 Address:  
        `ip -6 addr add 2001:db8::1/64 dev eth0    
        ifconfig eth0 inet6 add 2001:db8::1/64`  

Remove an IPv6 address:
        ip -6 addr del 2001:db8::1/64 dev eth0
        ifconfig eth0 inet6 del 2001:db8::1/64

Display existing IPv6 routes:
        ip -6 route show [dev eth0]
        route -A inet6

Add an IPv6 route through a gateway:
        ip -6 route add 2000::/3 via 2001:db8::1
        route -A inet6 add 2000::/3 gw 2001:db8::1

Removing an IPv6 route through a gateway:
        ip -6 route del 2000::/3 via 2001:db8::1
        route -A inet6 del 2000::/3 gw 2001:db8::1

Ann an IPv6 route through an interface:
        ip -6 route add 2000::/3 dev eth0 metric 1
        route  -A inet6 add 2000::/3 dev eth0

Remove an IPv6 route through an interface:
        ip -6 route del 2000::/3 dev eth0
        route -A inet6 del 2000::/3 dev eth0

Displaying neighbors using “ip”:
        ip -6 neigh show

Displaying existing tunnels
        ip -6 tunnel show
        route -A inet6 | grep "\Wsit0\W*$"

Change Ipv6 Default Priority (outgoing ip)
ip -6 addr change 2a02:20c8:1000::53:2 dev eth0 preferred_lft 0

LAB Discovery:
        ping6 -c4 -I eth0 ff02::1
        ip -6 neigh show


Create IPv6 Tunnel:
        modprobe ipv6
        ip tunnel add he-ipv6 mode sit remote 209.51.161.14 local 70.164.19.160 ttl 255
        ip link set he-ipv6 up
        ip addr add 2001:470:1f06:56f::2/64 dev he-ipv6
        ip route add ::/0 dev he-ipv6
