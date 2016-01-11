#Tuning a Linux Network Stack

For a high-performance system trying to serve thousands of concurrent network clients, default limits in a Linux system are far too low.

The parameters that requires tuning at a bare minimum are

- Increase Max open files - increasing this limit will ensure that lingering TIME_WAIT sockets and other consumers of file descriptors don’t impact our ability to handle lots of concurrent requests.

- Decrease the time that sockets stay in TIME-WAIT -  by lowering ```tcp_fin_timeout``` from its default of 60 seconds to 10. One can lower this even further, but too low, and you can run into socket close errors in networks with lots of jitter. We will also set tcp_tw_reuse to tell the kernel it can reuse sockets in the TIME_WAIT state.
 
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

#### Tuning Open File Descriptors
In addition to the Linux ```fs.file-max kernel``` setting above,increase the file descriptor limits. The reason is the above just sets an absolute max, but we still need to tell the shell what our per-user session limits are.

So, first edit /etc/security/limits.conf to increase our session limits:
```
# /etc/security/limits.conf
# allow all users to open 100000 files
# alternatively, replace * with an explicit username
* soft nofile 100000
* hard nofile 100000
```
Next, /etc/ssh/sshd_config needs to make sure to use PAM:
```
# /etc/ssh/sshd_config
# ensure we consult pam
UsePAM yes
```

And finally, /etc/pam.d/sshd needs to load the modified limits.conf:
```
# /etc/pam.d/sshd
# ensure pam includes our limits
session required pam_limits.so
```
Run ```ulimit -n``` to see the file descriptor set to the above value.

#### TCP Congestion Window
Finally, let’s increase the TCP congestion window from 1 to 10 segments. This is done on the interface, which makes it a more manual process that our sysctl settings. First, use ip route to find the default route, shown in bold below:
```
 ip route
default via 10.0.1.1 dev eth0  proto static 
10.0.1.0/24 dev eth0  proto kernel  scope link  src 10.0.1.193  metric 1 
192.168.122.0/24 dev virbr0  proto kernel  scope link  src 192.168.122.1 
```
Run the following command to change the ip route , adding ```initcwnd 10``` at the end
```
$ sudo ip route change default via 10.0.1.0/24 dev eth0  proto kernel initcwnd 10
```
To make this persistent across reboots, you’ll need to add a few lines of bash like the following to a startup script somewhere. Often the easiest candidate is just pasting these lines into <b> /etc/rc.local:</b>
```
defrt=`ip route | grep "^default" | head -1`
ip route change $defrt initcwnd 10
```
Once completed all these changes, bundle a new machine image, or integrate these changes into a system management package such as Chef or Puppet.

#### Resources
- [Linux Network Tuning 2013](http://www.nateware.com/linux-network-tuning-for-2013.html#.VBjahC5dVyE)
- [US Dept of Energy Guide to Linux TCP Tuning](http://fasterdata.es.net/host-tuning/linux/)
- [Linux tuning parameters used by Last.fm](https://russ.garrett.co.uk/2009/01/01/linux-kernel-tuning/)
- [Definitions of Linux TCP kernel variables](https://www.frozentux.net/ipsysctl-tutorial/chunkyhtml/tcpvariables.html)
- [Understanding ephemeral ports](http://www.ncftp.com/ncftpd/doc/misc/ephemeral_ports.html)
- [In-depth post by CDN Planet on TCP slow start (with tests!)](http://www.cdnplanet.com/blog/tune-tcp-initcwnd-for-optimum-performance/)
- [Google Research Paper Proposing a Default Congestion Window of 10 Segments](http://research.google.com/pubs/pub36640.html)
- [Determining a safe value for tcp_tw_reuse (ServerFault)](http://serverfault.com/questions/234534/is-it-dangerous-to-change-the-value-of-proc-sys-net-ipv4-tcp-tw-reuse)
- [Dropping of connections with tcp_tw_recycle (StackOverflow)](http://stackoverflow.com/questions/8893888/dropping-of-connections-with-tcp-tw-recycle)
- [Tuning servers for load testing with Bender](https://github.com/pinterest/jbender)

