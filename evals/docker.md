# Docker Issues

1. Breaking changes and regressions.

2. Cannot clean old images (No API support)
  The only way to do so, is a hack
  ```bash
  docker images -q -a | xargs --no-run-if-empty docker rmi
  ```
3. lack of native kernel support - there are numerious issues between interactions between kernel distribution, docker and the file system.
  The Linix kernel officailly dropped docker support.

4. Docker Registry - there are three versions of the registry and a clainet can pull from any one of them.

5. Saving State - docker is mean to be stateless therefore storing or running any process within docker that needs state such as databases is dangerous.  Containers are not meant to store data. Actually, they are meant by design to NOT store data.
  A crash would destroy the database and affect all systems connecting to it. It is an erratic bug, triggered more frequently under intensive usage. A database is the ultimate IO intensive load, that’s a guaranteed kernel panic. Plus, there is another bug that can corrupt the docker mount (destroying all data) and possibly the system filesystem as well (if they’re on the same disk).

  Nightmare scenario: The host is crashed and the disk gets corrupted, destroying the host system and all data in the process.

[](https://thehftguy.wordpress.com/)
