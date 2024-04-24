---
layout: post
title: Running the Bitcoin core daemon as a Docker container
permalink: /posts/running-the-bitcoin-core-daemon-as-a-docker-container
---

At [Justcoin Exchange](https://justcoin.com) we use a lot of Chef recipes to manage servers and deploy code. While chef has its advanteges, such as being very popular, it’s insanely slow. In this post I’ll investigate Docker under CoreOS as a faster and more reliable alternative.

![Docker](/assets/img/posts/2014-09-15-bitcoin-docker/docker.png)

**Docker** uses the containment features of Linux to run on top of a kind of differential file system. Docker containers are extremely fast to launch and can be modified incrementally.

![Core OS](/assets/img/posts/2014-09-15-bitcoin-docker/core-os.png)

**CoreOS** is a minimal Linux distribution that comes with systemd (service manager), etcd (distributed key-value store), fleet, distributed service manager, and of course Docker.

![AWS](/assets/img/posts/2014-09-15-bitcoin-docker/aws.png)


Attaching an Amazon EC2 EBS volume from Bash
--------------------------------------------

Docker containers are disposed when stopped. We’ll need to store the blockchain outside of the container. Storing the blockchain on the host limits us to a single host machine. To overcome this limitation we store the blockchain on an Amazon AWS EBS volume and attach the volume to the host before starting the container. This is done by using a [sidekick](https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/#run-a-simple-sidekick):

{% gist cf4697cba536c1fdb35e %}

The script looks up its own instance ID using Amazon AWS and attaches the specified volume to itself. When receiving SIGTERM/SIGINT, which is caused by running _docker stop_ from the host, it detaches the drive and exits gracefully. The source is [available on Github](https://github.com/abrkn/docker-ebs-sidekick) and the Docker repository on [Docker Hub.](https://registry.hub.docker.com/u/abrkn/ebs-sidekick/)

Attaching an instance to an Amazon AWS ELB
------------------------------------------

As mentioned before the bitcoind container can run on any host. An elastic load balancer will provide a consistent endpoint for speaking with the container.

{% gist efe4d71adbfc3d1fb472 %}

The ELB script is quite a bit simpler. It attaches (registers) the calling instance on start and detaches (deregisters) before exiting.

Running bitcoind inside Docker
------------------------------

I’ve also made a very simple Dockerfile for running bitcoind:

{% gist 9bf57465cbc2d7fa3601 %}

Piecing everything together
---------------------------

We now have three pieces of the puzzle; the bitcoind container, permanent storage and service discovery. I’m storing my AWS key and secret in etcd. Here’s the final Dockerfile:

{% gist ef10322c3a84f5959d01 %}

The code is currently only used in our testing environment and might have some bugs we’ll find before moving to production.
