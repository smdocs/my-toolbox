# Docker Issues

1. Breaking changes and regressions.

2. Cannot clean old images (No API support)
The only way to do so, is a hack
```bash
docker images -q -a | xargs --no-run-if-empty docker rmi
```

3. lack of native kernel support - there are numerious issues between interactions between kernel distribution, docker and the file system.
The Linix kernel officailly dropped docker support.


[](https://thehftguy.wordpress.com/)
