---
layout: post
title:  "Interacting with Docker containers"
---

Decided to take a break from C# programming on [Wreck.NET][wreck-net] today, and
went back to studying [Docker][docker].

One thing that has always confused me about the multiple ways of interacting 
with Docker containers. This is a common task for developers seeking to inspect
the state of a container's file system, networking and running processes.

The table below summarizes the different commands one may choose.

| **Mode**             | **Output** | **Input** | **New container**                | **Running container**                | **Same Running Container**                |
|----------------------|------------|-----------|----------------------------------|--------------------------------------|-------------------------------------------|
| Foreground (default) | Y          | N         | docker run <image>               | docker exec <name / id> <command>    | docker attach <name / id> / Ctrl-C        |
| Interactive          | Y          | Y         | docker run -it <image> /bin/bash | docker exec -it <name / id>          | docker attach <name / id> / Ctrl-p Ctrl-q |
| Background           | N          | N         | docker run -d <image>            | docker exec -d <name / id> <command> |                                           |

# How `attach` and `exec` Differ

As a side-note, there is a good explanation explaining the differences between
[attaching to a container versus executing a session within a container][attach-vs-exec].

While it goes down to the guts and gory details of how `docker exec` and 
`docker attach` is actually implemented, which for most users do not matters, so
I shall only focus on the differences and implications from the end-user 
perspective as a developer when choosing `attach` or `exec`.

The main difference is that `exec` creates a temporary invisible container that 
shares the specified container's resources, giving the illusion of running a 
command in that container. Whereas `attach` actually runs the command directly
inside the container.

The implication is that if we `exec` a `/bin/bash` session and then exit it by
choice or accident, the original detached running container keeps running. 
Simply because it is running in a temporary container as a different process.

If we `attach` to a running container, we are directly interacting with the 
running process in that container. In cases where we press Ctrl-C or type `exit`
at the shell prompt when we are attached to the container's running process -- 
whether consciously or by mistake -- it will end the process and its container.

This is the typical behaviour we can expect.

# TLDR: The Bottomline

The bottomline when we don't have much time to study Docker in-depth is: 

- Use `exec` to execute and run a one-off command within a container.
- Use `exec -it /bin/bash` if we want an interactive session.
- Use `docker stop` to stop and end the running process/container from the host. 
  There is no need to use Ctrl-C and `attach` to stop the process in the 
  foreground.
- Always run containers with `-d` in detached mode in the background like in 
  production.
- If we just want to see the outputs from the container, use `docker logs`.

# Rants and Criticisms of `attach`

All these said, I have my criticisms about `attach`. The `attach` option seems 
like it exists due to implementation, but without a real use case for users. I 
wonder why Docker is keeping it.

In most cases, we do not need to use `attach`. Just ignore it. The presence of
`attach` seems to me an unnecessary distraction that only confuses people by 
adding complexity without an unique use case that demands it. `attach` connects 
to the main running process. If it's a shell, we get an interactive prompt when 
we attach to it. If it isn't, we get the scrolling outputs from the running 
process. All of these can be achieved with `docker exec` without forcing the 
users think hard about how to redirect and map their `stdin`, `stdout` and 
`stderr` IO streams, or having to understand the teeny weeny difference between
`attach` and `exec`.

Trying to use `attach` properly seems like it depends on the phase of the
moon because its behaviour is dependent on several factors. For example, whether
the container was started in detached mode or not, whether the main process is a 
interactive shell or some other non-interactive one. All of which affects 
what a user actually experiences.

We just need **one consistent method that always works**. It does not help 
that in different user guides, articles and tutorials, their author choose to 
use `attach` or `exec` without explaining why adding to the confusion in new 
learners. Not many has time to carefully study the inner details of how Docker
is actually implemented unless one is a Docker developer.
  

[wreck-net]: https://github.com/gyk4j/wreck-net
[docker]: https://www.docker.com/
[attach-vs-exec]: https://iximiuz.com/en/posts/containers-101-attach-vs-exec/