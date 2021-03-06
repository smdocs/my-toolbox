#Linux Troubleshooting Tools

- ### Standard Linux Tools

  ##### 1. ```strace```
  
  | Operation       | strace command         | Notes | 
  | --------------  |------------------------| ----------|
  | Trace the execution of a command | ```$ strace ls``` | Command to trace a command |
  | Trace only when certain/specific system calls are made | ```$ strace -e open ls OR $ strace -e open,read ls  ``` | 
  | Save a trace file | ```$ strace -o output.txt ls``` | Save the output od strace in output.txt | 
  | Watch a running process with PID=1234 | ```$ strace -p 1234 ```| Attaches to a runnning process wit ID 1234 |
  | Print a timestamp for each output line of the trace  | ```$ strace -t ls ```| Prints timestamp |
  | Print relative time for system calls  | ```$ strace -r ls ```| |
  | Generate batch statistics reports of system calls   | ```$ strace -c ls ```|  |
  | Generate live, per-second statistics reports of system calls for running process with PID=1234| ``` use sysdig```| | 
  
  
  ##### 2. ```lsof```
  | Operation       | strace command         | Notes | 
  | --------------  |------------------------| ----------|
  |List all open files for a specific process with PID=1234 | ```$ lsof -p 1234``` | Pipe to grep if you a looking for any specific pattern  |
  |List all network connections | ```$lsof -i OR lsof -i tcp OR lsof -i idp``` | Provide tcp/udp to see all tcp/udp connections|
  |List network connections in use by a specific process with  PID=1234 | ```$ lsof -i -a -p 1234``` |  |
  |List processes that are listening on port 22| ```$ lsof -i :22``` | |
  |List processes that have opened the specific file /var/log/syslog |```$ lsof /var/log/syslog```| |
  |List processes that have opened files under the directory /var/log|```$ lsof +d  /var/log ```| |
  |List files opened by processes named “sshd”| ```$ lsof -c sshd```| |
  |List files opened by a specific user named “jdoe”| ```$ lsof -u jdoe```| Use ```lsof -u ^jdoe``` to exclude jdoe|
  
  
  ##### 3. ```htop```
  
  ##### 4. ```tcpdump```
  
  tcpdump is focused entirely on network traffic, while network traffic is only a subset of what sysdig covers. Many tcpdump use cases involve filtering, and tcpdump uses network-specific BPF filters.
  
  | Operation       | strace command         | Notes | 
  | --------------  |------------------------| ----------|
  |Capture packets from a particular interface eth0 (192.168.10.119) | ```$ tcpdump -i eth0	``` ||
  |Capture only 100 packets	| ```$ tcpdump -c  100``` | |
  |Display captured packets in ASCII	 | ```$tcpdump -A ``` |  |
  |Display captured packets in HEX and ASCII| ```$ tcpdump -XX``` | |
  |Capture packet data, writing it into into a file |```$ tcpdump -w saved.pcap```| |
  |Read back saved packet data from a file|```$ tcpdump -r saved.pcap	```| |
  |Capture only packets longer/smaller than 1024 bytes| ```$ tcpdump greater 1024```| |
  |Capture only UDP or TCP packets| ```$ tcpdump udp```| Use ```tcpdump tcp``` to include tcp|
  |Capture only packets going to/from a particular port|```$tcpdump port 22```||
  
  
  ##### 5. ```iftop```

- ### Other Tools

[Sysdig](http://www.sysdig.org/)
[Sysdig Cheatsheet](https://sysdig.com/blog/linux-troubleshooting-cheatsheet/)

