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
  
  ##### 3. ```htop```
  
  ##### 4. ```tcpdump```
  
  ##### 5. ```iftop```

- ### Other Tools

[Sysdig](http://www.sysdig.org/)
[Sysdig Cheatsheet](https://sysdig.com/blog/linux-troubleshooting-cheatsheet/)

