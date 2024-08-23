---
layout: post
title:  "Mult-Service Container's supervisord.conf"
---

Read up on [running multiple processes in a container][procman].

Quite surprised that the documentation did not bother to provide a full working
example, and was missing a `supervisord.conf` configuration file. After 
studying the provided `supervisord.conf` provided, it is clear that it only 
covers the configuration settings for logging files, and not the processes
being started.

I have reproduced the unchanged `Dockerfile` here.

```docker
# syntax=docker/dockerfile:1
FROM ubuntu:latest
RUN apt-get update && apt-get install -y supervisor
RUN mkdir -p /var/log/supervisor
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY my_first_process my_first_process
COPY my_second_process my_second_process
CMD ["/usr/bin/supervisord"]
```

Based on [supervisord's documentations][supervisord], the corresponding 
`supervisord.conf` for the example should look like this:

```ini
[supervisord]
nodaemon=true
logfile=/dev/null
logfile_maxbytes=0

[program:my_first_process]
command=my_first_process
redirect_stderr=false
stdout_logfile=/var/log/my_first_process/stdout.log
stdout_logfile_maxbytes=0
stderr_logfile=/var/log/my_first_process/stderr.log
stderr_logfile_maxbytes=0

[program:my_second_process]
command=my_second_process
redirect_stderr=false
stdout_logfile=/var/log/my_second_process/stdout.log
stdout_logfile_maxbytes=0
stderr_logfile=/var/log/my_second_process/stderr.log
stderr_logfile_maxbytes=0
```

Note that *all program settings are optional* except for `command` which is the
only required setting.

Hope this clears up some confusion.

[procman]: https://docs.docker.com/engine/containers/multi-service_container/#use-a-process-manager
[supervisord]: http://supervisord.org/configuration.html

