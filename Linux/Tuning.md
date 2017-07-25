### Linux sysctl security

```bash
# Do not accept ICMP redirects (prevent MITM attacks)
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
# _or_
# Accept IC

# Uncomment the next two lines to enable Spoof protection (reverse-path filter)
# Turn on Source Address Verification in all interfaces to
# prevent some spoofing attacks
net.ipv4.conf.default.rp_filter=1
net.ipv4.conf.all.rp_filter=1

# Do not accept ICMP redirects (prevent MITM attacks)
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
# _or_
# Accept ICMP redirects only for gateways listed in our default
# gateway list (enabled by default)
# net.ipv4.conf.all.secure_redirects = 1
#
# Do not send ICMP redirects (we are not a router)
net.ipv4.conf.all.send_redirects = 0
#
# Do not accept IP source route packets (we are not a router)
net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0
```

### Linux VM Tuning

```bash
# Enable window-scaling
net.ipv4.tcp_window_scaling = 1 # Denne er on by default i C7, men kan likevel settes explicitly

# Øke TCP Send / recv buffers. Buffers er alltid nice å ha svære om man har minne nok til det. 
net.core.rmem_max = 16777216 # 16 MB, default 200kb
net.core.wmem_max = 16777216 # 16 MB, default 200kb
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216

# Hindre / stoppe SYN floods
net.ipv4.tcp_syncookies = 1 # Off by default i C7
```



