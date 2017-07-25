```bash

# The number of incoming connections on the backlog queue. The maximum number of packets queued on the INPUT side.
net.core.netdev_max_backlog = 30000

# The size of the receive buffer for all the sockets. 16MB per socket.
net.core.rmem_max = 16777216

# The maximum number of queued sockets on a connection.
net.core.somaxconn = 16096

# The size of the buffer for all the sockets. 16MB per socket.
net.core.wmem_max = 16777216	

# On a typical machine there are around 28000 ports available to be bound to. 
# This number can get exhausted quickly if there are many connections. We will increase this.
net.ipv4.ip_local_port_range = 1024 65535	

# Usually, the Linux kernel holds a TCP connection even after it is closed for around two minutes. 
# This means that there may be a port exhaustion as the kernel waits to close the connections. By moving the fintimeout
# to 15 seconds we drastically reduce the length of time the kernel is waiting for the socket to get any remaining packets.
net.ipv4.tcp_fin_timeout = 15	

# Increase the number syn requests allowed. Sets how many half-open connections to backlog queue
net.ipv4.tcp_max_syn_backlog = 20480	

# Increase the tcp-time-wait buckets pool size to prevent simple DOS attacks
net.ipv4.tcp_max_tw_buckets = 400000	

# TCP saves various connection metrics in the route cache when the connection closes so
# that connections established in the near future can use these to set initial conditions. Usually, this increases overall
# performance, but may sometimes cause performance degradation.
net.ipv4.tcp_no_metrics_save = 1	

# (min, default, max): The sizes of the receive buffer for the IP protocol.
net.ipv4.tcp_rmem = 4096 87380 16777216	

# Number of times initial SYNs for a TCP connection attempt will be retransmitted for outgoing connections.
net.ipv4.tcp_syn_retries = 2	

# This setting determines the number of SYN+ACK packets sent before the kernel gives up on the connection
net.ipv4.tcp_synack_retries = 2	

# Security to prevent DDoS attacks. http://cr.yp.to/syncookies.html
net.ipv4.tcp_syncookies = 1	

# (min, default, max): The sizes of the write buffer for the IP protocol.
net.ipv4.tcp_wmem = 4096 65536 16777216	

# The max amount of file handlers that the Linux kernel will allocate. This is one part the other part is setting the ulimits.
proc.file-max = 2097152	

# Amount of memory to keep free. Don't want to make this too high as Linux will spend more time trying to reclaim memory.
proc.min_free_kbytes = 65536	

# Keep 64MB or Ram available at all times so if things are not working we can, at least, SSH to
# the system and do tasks and not get an out of memory error.
vm.min_free_kbytes = 65536	

```
