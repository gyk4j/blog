---
layout: post
title:  "Vagrant vs. Terraform: an alternative interpretation"
---

# Preface

I was curious about the differences between [Vagrant][vagrant] and 
[Terraform][terraform]. When would someone choose or prefer one technology over 
the other?

At their core, both are designed to automate the creation of virtual machines 
and the the recreation of computing environments. Pretty much the 
[Docker Compose][docker-compose] of virtual machines.

So like everyone, I googled and found an authoritative article from their 
creator and maintainer [HashiCorp][hashi-corp] aptly titled 
["Vagrant vs. Terraform"][vagrant-vs-terraform]. I read -- and had to re-read a 
couple of times -- nitpicking over the choice of words in order to spot the 
subtle differences and implications behind the jargons and terminologies.

# Criticisms of HashiCorp's explanation

Why is it difficult to understand you may ask. Let me quote a few examples.

## Example 1

> Vagrant is a tool focused for **managing development environments** and  
> Terraform is a tool for **building infrastructure**.

Isn't creating and recreating a development or test environment "building 
infrastructure" too?

## Example 2

> Terraform can describe **complex sets of infrastructure** that exist locally  
> or remotely.

Given a long enough `Vagrantfile` written in Ruby, perhaps it can also recreate
a similarly complex setup with multiple virtual machines? So what are the things
it cannot do that Terraform can? This does not elaborate further in details.

As a side note, I suspect the network configuration options in 
[Vagrant][vagrant] are more limited and less granular than those available in 
[Terraform][terraform] which allow users to explicitly describe complex 
networking between virtual machines in different cloud service providers, though
this needs to be confirmed. 

## Example 3

> Terraform is focused on **infrastructure management** and not 
> **development environments**

People may be wondering how different a production environment and development 
environment can be. In some cases, developers try to recreate the development 
environment as closely as possible to the real thing, to the point of creating 
an exact duplicate clone setup. It all depends on the simplified adaptations
adopted for a development setup. And these differ between every organization, 
system and project.

For users whose development, testing, staging and production environments are
exact replicas or clones of one another, would it make a difference between 
choosing Vagrant or Terraform? And what are the implications for choosing one 
over the other?

These are unanswered.

## Example 4

> Vagrant provides a number of higher level features that Terraform doesn't. 
> Synced folders, automatic networking, HTTP tunneling, and more are features 
> provided by Vagrant to ease development environment usage.

This is because it is assumed that the virtual machines are running locally on 
one single development machine host, which allows all these automated network
configuration and storage volume/network shares mounting (synced folders). The
assumption is not stated.

## Example 5

Finally, it finishes off by concluding that:

> The primary usage of Terraform is for managing remote resources in cloud 
> providers such as AWS. Terraform is designed to be able to manage extremely 
> large infrastructures that span multiple cloud providers. Vagrant is designed 
> primarily for local development environments that use only a handful of 
> virtual machines at most.
>   
> Vagrant is for **development environments**. Terraform is for more general 
> **infrastructure management**.

The final two paragraphs tell us what we actually need to know, and I agree this
main message is helpful: Terraform is for large production cloud/remote 
environment, and Vagrant is for small local development environment. 

Still, people are left to wonder how "extremely large" of an infrastructure it 
should be to definitely choose Terraform over Vagrant. And what do we mean by 
"a handful"? Two, five or ten VMs? And is managing development environment part
of **infrastructure management**? The clarity is not there.

## Reading between the lines

My conclusion: it is not easy to read and understand for anyone new to virtual 
machines, cloud computing and business users. The differences are not obvious
and we have to read between the lines carefully despite HashiCorp's attempt at
explaining the differences to help people decide which one is for them. 

Whether it is carefully worded to obfuscate the differences or it happens to be 
a lame attempt at clarifying the differences, I certainly hope that HashiCorp 
can improve its documentations and communication with its customers and users.

A clear explanation just needs *one line or paragraph* that summarizes the core 
information to help people make a decision which one is for them.

This blog post is my own interpretation to help people interested in Vagrant and
Terraform to decide which one is more suitable for their use cases.

# The Real Differences

In short, the differences are:

| Aspect               |                                             **Vagrant**                                            |                                                        **Terraform**                                                         |
|----------------------|:--------------------------------------------------------------------------------------------------:|:----------------------------------------------------------------------------------------------------------------------------:|
| Year                 | 2012                                                                                               | 2014                                                                                                                         |
| Language             | Ruby                                                                                               | YAML                                                                                                                         |
| Targeted user        | Developers                                                                                         | Deployment / operations engineers                                                                                            |
| Use case             | Creating development enviroment on a single local machine                                          | Creating production and test environments having virtual machines potentially spanning multiple cloud and on-premise servers |
| Host Virtual Machine | Local developer workstation or desktop PC                                                          | Cloud service providers, remote data centers or on-premise servers                                                           |
| Hypervisor           | Type 2 (e.g. Oracle VirtualBox, VMWare Fusion/Workstation, Microsoft Virtual PC, Parallel Desktop) | Type 1 (e.g. VMWare vSphere/ESXi, Microsoft Hyper-V, KVM, Xen)                                                               |

Note that this is a simplication to compare and contrast to help people make a 
decision.

# Disclaimer for edge and atypical use cases

Certainly, there could be cases where people install a type 1 hypervisor on a 
workstation or desktop PC for small scale experimentation or repurposing 
consumer grade hardware for a home server. And this opens up the door to test-
drive Terraform on a local machine.

And there could be people deploying a *simple setup* consisting of one virtual 
machine instance to a public cloud provider like Amazon, Azure or Google Cloud
Platform. In this case, [Vagrant][vagrant] can possibly
[achieve this too with the right plug-in][vagrant-aws], as well as 
[Terraform][terraform].

Or at the other extreme end of the spectrum, people writing long elaborate 
`Vagrantfile` just to mimic a very complex setup running on a type 2 hypervisor 
on a single host. That single virtual machine host may well be a souped-up 
machine with dual Intel Xeon or AMD EPYC processors paired with 128 GB of RAM, 
enough to run quite a few virtual machines concurrently. Or some old rack or 
blade server lying on someone's table?

Who can predict what creative people would attempt to do with what they have or 
can afford?

And then, it may boil down to user's preferred language (Ruby or YAML) to 
describe their virtual machine environment.

# Conclusion

[Vagrant][vagrant] and [Terraform][terraform] are both good products in their 
own right, and each has their own place. It is just a pity that 
[HashiCorp][hashi-corp] has not made it easy for people to decide at one glance
which one is more suitable for them.

I understand that [Vagrant][vagrant] is an open source community-driven 
technology that [HashiCorp][hashi-corp] does not make money from. Instead it has
the incentive to push for and promote [Terraform][terraform] which actually 
brings it money. This may be the business reason behind its less proactive 
attitude toward it.

Hopefully, my own overly-simplified (and perhaps less accurate) personal 
interpretation can help some people to decide which technology is more suitable 
for them to investigate for learning and adoption.

[vagrant]: https://www.vagrantup.com/
[terraform]: https://www.hashicorp.com/products/terraform
[docker-compose]: https://docs.docker.com/compose/
[hashi-corp]: https://www.hashicorp.com/
[vagrant-vs-terraform]: https://developer.hashicorp.com/vagrant/intro/vs/terraform
[vagrant-aws]: https://devops.com/devops-primer-using-vagrant-with-aws/