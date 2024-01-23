---
layout: post
title:  "Using Vagrant with Docker"
---

Someone asked if [Vagrant (or virtual machine in general) is dead][is-vagrant-dead], 
and is it still worth learning.

My opinion is it is still not completely dead, and still relevant for some edge 
cases, some of which I shall describe below.

However, I must emphasize that for most common use cases, containers can and 
should replace virtual machines. In fact, the reason for most virtual machines'
existence is simply there was no Docker container technology -- and a better 
choice -- back then when they were created 10 to 20 years ago. Most virtual 
machines were created as a self-contained environment to encapsulate a single 
software application. The intention was to reduce dependencies and the chance 
that changes to system state caused by other software applications would affect 
its proper functioning.

# Mix of Windows and Linux systems

One obvious thing that we still need VMs is when our environment has a mix of 
Windows and Linux systems (and perhaps other operating systems). 

## Option A: Use virtual machines for different operating systems

One thing that Docker on Linux cannot do is to mix operating system kernels, so 
we may have to keep these systems as virtual machines, and Vagrant and Terraform
to manage them.

## Option B: Use a Windows container host

An alternative is to use [Windows Subsystem for Linux (WSL 2)][wsl] on Windows 
10 or Windows Server for that. Choosing this option allows one to keep 
everything as containers, but at the expense of having to run them on a 
Windows-based container host. If Microsoft and Windows are not a strictly no-go 
for you, it may be an option.

# Security requirements

Virtualization offers higher security isolation between virtual machines. If you
have high security requirements for certain mission-critical systems, or for 
processing sensitive or classified data, the security offered by containers may 
not be sufficient.

So in this case, we still have to keep them as virtual machines. In extreme 
cases, the regulatory requirements may even mandate physcial separation using
different bare-metal machines, which even virtual machines may not suffice.

# Advanced networking

Generally, the networking options available with Docker is more limited than 
those from virtualization. By default, Docker offers only 
*bridged, host, ipvlan, macvlan and none* networking.

Virtualization like VirtualBox offers additional fine-grained options like 
*internal* networking, which does not have an equivalent on Docker, with the
closest option as *user-defined bridge* networking.

The networking modes that are roughly analogous to each other are mapped out
below:

|       **Networking Mode**      |           **VirtualBox**          |      **Docker**     |                                    **Notes**                                    |
|--------------------------------|:---------------------------------:|:-------------------:|:-------------------------------------------------------------------------------:|
| No network                     | Not attached                      | None                |                                                                                 |
| DNS routing                    | Network Address Translation (NAT) | Bridge              | DNS and routing to different bridges' IP subnet                                 |
| Host IP subnet                 | Bridged networking                | Host                | In host IP address subnet                                                       |
| VMs/Containers subnet          | Internal networking               |                     | No equivalent option to block/isolate host <-> container access?                |
| Host and VMs/Containers subnet | Host-only networking              | User-defined bridge | In bridge-specific subnet to isolate containers in different bridge subnet      |
| IP VLAN                        | VDE networking                    | ipvlan              | VDE networking is not available by default, and is only for Linux/FreeBSD hosts |
| MAC VLAN                       | Bridged networking                | macvlan             | Set virtual NIC MAC in **Settings > Network > Adapter > MAC Address**           |
| Between hosts                  | VDE networking                    | overlay             | Overlay is more for unique Swarm clustering use                                 |

[is-vagrant-dead]: https://www.reddit.com/r/devops/comments/v052wc/is_vagrant_dead/
[wsl]: https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux