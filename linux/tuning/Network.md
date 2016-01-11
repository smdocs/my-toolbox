#Tuning a Linux Network Stack

For a high-performance system trying to serve thousands of concurrent network clients, default limits in a Linux system are far too low.

The parameters that requires tuning at a bare minimum are

- Increase Max open files 
- Decrease the time that sockets stay in TIME_WAIT
- Increase the port range for ephemeral (outgoing) ports
- Increase the read/write TCP buffers (tcp_rmem and tcp_wmem) to allow for larger window sizes.
- Decrease the VM swappiness parameter, which discourages the kernel from swapping memory to disk.
- Increase the TCP congestion window, and disable reverting to TCP slow start after the connection is idle.

#### Edit the following kernel parameters in /etc/sysctl.conf ( Don't forget to back up the default file )

```bash
# /etc/sysctl.conf
# Increase system file descriptor limit
fs.file-max = 100000

# Discourage Linux from swapping idle processes to disk (default = 60)
vm.swappiness = 10

# Increase ephermeral IP ports
net.ipv4.ip_local_port_range = 10000 65000

# Increase Linux autotuning TCP buffer limits
# Set max to 16MB for 1GE and 32M (33554432) or 54M (56623104) for 10GE
# Don't set tcp_mem itself! Let the kernel scale it based on RAM.
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.core.rmem_default = 16777216
net.core.wmem_default = 16777216
net.core.optmem_max = 40960
net.ipv4.tcp_rmem = 4096 87380 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216

# Make room for more TIME_WAIT sockets due to more clients,
# and allow them to be reused if we run out of sockets
# Also increase the max packet backlog
net.core.netdev_max_backlog = 50000
net.ipv4.tcp_max_syn_backlog = 30000
net.ipv4.tcp_max_tw_buckets = 2000000
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 10

# Disable TCP slow start on idle connections
net.ipv4.tcp_slow_start_after_idle = 0

# If your servers talk UDP, also up these limits
net.ipv4.udp_rmem_min = 8192
net.ipv4.udp_wmem_min = 8192

# Disable source routing and redirects
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.accept_source_route = 0

# Log packets with impossible addresses for security
net.ipv4.conf.all.log_martians = 1
```
Since some of these settings can be cached by networking services, it’s best to reboot to apply them properly (sysctl -p does not work reliably).
