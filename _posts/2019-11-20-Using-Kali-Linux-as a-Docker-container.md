---
layout: post
title: "Using Kali Linux as a Docker container"
description: Learn Docker for pentesing
date:   2019-11-11
image:  "img/Docker-Kali.png"
tags: ["Aprende Docker", "Penetration Testing", "Kali Linux"]
---

Hello Hackers!!  
Not all Kali Linux tools are easy or possible to install on another operating system, so it is common to use a bootable USB or a VM to use them, but there is another solution such as using the official Kali Linux container for Docker.  

To install, I recommend reading the following [official docker instructions](https://docs.docker.com/install/).  

After having docker installed, the next step is to download the official image of Kali Linux.  
To do this we execute the following:  

{% highlight zsh %}
┌─[root@Spartan] - [/tmp] - [jue nov 21, 12:13]
└─[$]> docker pull kalilinux/kali-linux-docker
Using default tag: latest
latest: Pulling from kalilinux/kali-linux-docker
014a6d74f96c: Pull complete
9febb14563a0: Pull complete
c38f04972c6b: Pull complete
9d39d049d5d0: Pull complete
4e80058918bf: Pull complete
Digest: sha256:196554d5bd127e7ddee846ee2b9453400c2b40e8ca646e1400d
Status: Downloaded newer image for kalilinux/kali-linux-docker:latest
docker.io/kalilinux/kali-linux-docker:latest
{% endhighlight %}

We will proceed by creating the container with the following parameters:  

{% highlight bash %}
┌─[root@Spartan] - [/tmp] - [jue nov 21, 13:12]
└─[$]> docker run --name kali_linux --net="host" --privileged -e DISPLAY=$DISPLAY -it -v /tmp/.X11-unix:/tmp/.X11-unix kalilinux/kali-linux-docker /bin/bash

root@cs-6000-devshell-vm-db41a83e-bf48-4c25-bdce-8c3d9400c746:/# cat /etc/issue
Kali GNU/Linux Rolling 
{% endhighlight %}

**--name** this parameter names the container.  

**--net="host"** this parameter is used to make the programs inside the Docker container look like they are running on the host itself, from the perspective of the network. It allows the container greater network access than it can normally get.  

**--privileged** along with **--net** It allows you to place the network interface in monitor mode and use the host content hardware such as bluetooth.  

**-e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix** these two parameters allow applications that require a graphic server to be run using the host's.  

**-it** This parameter allows the container to run in interactive mode in the current tty.  

{% highlight bash %}
docker run --name kali_linux --net="host" --privileged -e DISPLAY=$DISPLAY -it -v /tmp/.X11-unix:/tmp/.X11-unix kalilinux/kali-linux-docker /bin/bash
{% endhighlight %}

Kali Linux only provides a base image of Docker so it is necessary to install the rest of the packages using apt.  

The complete reference as well as the download sizes of the metapackages is available on the [Kali Linux site](https://www.kali.org/news/kali-linux-metapackages/).  

For example, to install the Kali-Linux metapackage we must execute the following:  

{% highlight bash %}
┌─[root@Spartan] - [/tmp] - [jue nov 21, 13:12]
└─[$]> apt-get update && apt-cache search kali-linux
{% endhighlight %}

## Conclusion
Docker is an alternative not to depend on a USB memory, dual boot or a VM to use Kali Linux, in addition this form of installation allows you to use the tools as if they were part of the host where the container is located, similar to how they work applications with Snap.
