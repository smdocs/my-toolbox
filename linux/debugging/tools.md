#Linux Troubleshooting Tools

- ### Standard Linux Tools

  ##### 1. ```strace```
  
  | Operation       | strace command         | Notes | 
  | --------------  |------------------------| ----------|
  | Trace the execution of a command | ```$ strace ls``` | Command to trace a command |
  | Trace only when certain/specific system calls are made | ```$ strace -e open ls OR $ strace -e open,read ls  ``` | 
  | Save a trace file | ```$ strace -o output.txt ls``` | Save the output od strace in output.txt | 
  
  ##### 2. ```lsof```
  
  ##### 3. ```htop```
  
  ##### 4. ```tcpdump```
  
  ##### 5. ```iftop```

- ### Other Tools

[Sysdig](http://www.sysdig.org/)
[Sysdig Cheatsheet](https://sysdig.com/blog/linux-troubleshooting-cheatsheet/)

