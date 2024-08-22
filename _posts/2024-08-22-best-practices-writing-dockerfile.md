---
layout: post
title:  "Best practices for writing Dockerfiles"
---

I chanced upon a nice article covering 
[the use of RUN, CMD and ENTRYPOINT by Jay Schmidt][run-cmd-entrypoint] 
while revisiting the use of Docker. I have condensed my understanding from the 
article into a summary of key takeaways, lessons, best practices and personal 
rules.

> [!NOTE]
>
> I *am not* covering best practies for Docker instruction ordering, layers or 
> image size optimization in this post as I only focus on `RUN`, `ENTRYPOINT` 
> and `CMD`. They should also be considered for advanced users seeking optimal
> Docker image building.

# Difficulties and Challenges

The choice of using `RUN`, `CMD` and `ENTRYPOINT` has always been a constant 
source of confusion for newcomers to Docker -- including me. There have been
attempts to explain and clarify their usage -- most of which have failed -- and
they involve careful reading due to the way these instructions have been 
designed.

To add to the confusion, there is a related issue of having to choose between 
`shell` and `exec` form of writing these instructions, which doubles the 
potential combination of ways to express an identical idea.

Plus, the lack of consistency between Dockerfile writers and Docker image
creators has resulted in some uses `CMD` over `ENTRYPOINT`. While both may work 
under certain circumstances, many are left confused which one to choose.

This valuable [article][run-cmd-entrypoint] brings clarity to this issue with 
an in-depth discussion. A careful reading is recommended if one can spare the 
time to do so.

# Best Practices

Below are the rules-of-thumb to quickly guide me in deciding when to use `RUN`, 
`CMD` or `ENTRYPOINT`, and choosing the `shell` or `exec` form when writing 
them. 

Let me stress that it is a matter of personal preference and self-discipline 
when it comes to adopting them.

* Rule #1: For running commands to customize a Docker image during build time, 
use `RUN`.
* Rule #2: For starting a program or script as the main and sole process with 
PID 1, always use `ENTRYPOINT`.
* Rule #3: For some additional optional command line arguments that we
allow the user to add or override, use `CMD`.
* Rule #4: Use `shell` form only when the command involves shell variable 
expansion (e.g. for name globbing), use of environment or shell variables (e.g.
$HOME, $USER, $PATH), or piping or redirection (e.g. \|, \>, \>\>).
* Rule #5: Use `exec` form by default to run without a subshell, especially for 
`ENTRYPOINT` instruction. This ensures the main process is PID 1 that handles
signals properly from the Docker host.

# Common Mistakes or Bad Practices

## Observation #1: Using `CMD` for starting process instead of `ENTRYPOINT`

While it may work, the command can and may be accidentally overridden by
users leading to mysterious error from a missing executable or script. The path 
and name of the executable or script has to be specified by user if overridden, 
which in all likelihood, a Docker image user would not know unless one digs 
into the Dockerfile to figure out. For example, when doing a docker run, it has 
to be run like 
`docker run <image> <executable/script> <other additional command line args>`,
instead of just
`docker run <image> <other additional command line args>` if `ENTRYPOINT` is
used instead.

## Observation #2: Using `shell` form over `exec` form

While it may be convenient for converting shell commands without having to 
enclose them in brackets and quotes in the `exec` form, instructions in shell
form are run in a subshell that does not handle siganals (e.g. `SIGTERM`)from 
Docker host properly for a clean and graceful process termination.

# Processing Logic in Pseudocode 

Deep within Docker, I am guessing the logic may look like this when it comes to 
parsing and interpreting the instructions. I have chosen to write in pseudocode 
for the sake of brevity simply to demonstrate the logic behind it.

```
string command = ""

if running Docker build:
  if instruction is "RUN":
    if following string tokens are in shell form:
      prepend subshell "/bin/sh -c" to command
      
else if running Docker run:
  
  if instruction is "CMD":
    if following string tokens are in shell form:
      prepend subshell "/bin/sh -c" to command
    append following string tokens to command 
    
  if instruction is "ENTRYPOINT":
    if following string tokens are in shell form:
      prepend subshell "/bin/sh -c" to command
    insert following string token in command before other cmd
    
execute command
```

# Discussion and Recommendations

So the fact that using `CMD` works is more of a hack rather than its intended 
proper usage by design. However, given that today many Dockerfiles are misusing
it, it is a problem that Docker does not wish to correct by enforcing proper
use of `CMD` and `ENTRYPOINT`. Doing so would probably break many existing
Docker images. So, my suggestion would be to start using them in the right way
instead, especially when learning to use Docker.

Nobody would stop one from always using `CMD` and the `shell` form because they 
look easy and familiar. The Docker containers would still run and not handling
signals may not cause any problem in some situations.

But learning to do things the right way by design from the beginning is always 
better in my opinion. Feel free to disagree with me, no offence intended.

[run-cmd-entrypoint]: https://www.docker.com/blog/docker-best-practices-choosing-between-run-cmd-and-entrypoint/