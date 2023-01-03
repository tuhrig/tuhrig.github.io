---
layout: post
title: "How to know you are inside a Docker container"
date: "2014-05-10"
categories: 
  - "devops"
  - "cloud"
  - "docker"
---

How do you know that you are living in the Matrix? 
Well, I do not know, but at least I know how to tell you if you are inside a [Docker](https://www.docker.com/) container or not.

## The Docker Matrix

Docker provides virtualization based on _Linux Containers_ ([LXC](https://linuxcontainers.org/)). 
LXC is a technology to provide operating system virtualization for processes on Linux. 
This means, that processes can be executed in isolation without starting a real and heavy virtual machine. 
All processes will be executed on the same Linux kernel, but will still have their own namespaces, users and file system.

An important feature of such virtualization is that applications inside a virtual environment do not know that they are not running on real hardware. 
An application will see the same environment, no matter if it is running on real or virtual resources.

## /proc

However, there are some tricks. 
The `/proc` file system provides an interface to kernel data structures of processes. 
It is a pseudo file system and most of it is read-only. 
But every process on Linux will have an entry in this file system (named by its `PID`):

```
# every process ID has its sub-directory in /proc
/proc/[pid]
```

In this directory, we find information about the executed program, its command line arguments and working directory. 
And since the Linux kernel 2.6.24, we also find a file called `cgroup`:

```
# since Linux 2.6.24 we find a cgroup file there
/proc/[pid]/cgroup
```

This file contains information about the control group the process belongs to. 
Normally, it looks something like this:

```
vagrant@ubuntu-13:~$ cat /proc/1/cgroup
11:name=systemd:/
10:hugetlb:/
9:perf_event:/
8:blkio:/
7:freezer:/
6:devices:/
5:memory:/
4:cpuacct:/
3:cpu:/
2:cpuset:/
```

But since LXC (and therefore Docker) makes use of `cgroups`, this file looks different inside a container:

```
vagrant@ubuntu-13:~$ docker run busybox cat /proc/1/cgroup
11:name=systemd:/
10:hugetlb:/
9:perf_event:/
8:blkio:/
7:freezer:/
6:devices:/docker/3601745b3bd54d9780436faa5f0e4f72bb46231663bb99a6bb892764917832c2
5:memory:/
4:cpuacct:/
3:cpu:/docker/3601745b3bd54d9780436faa5f0e4f72bb46231663bb99a6bb892764917832c2
2:cpuset:/
```

As you can see, some resources (like the CPU) belong to a control group with the name as the container. 
We can make this a bit easier if we use the keyword `self` instead of the `PID`. 
The keyword `self` will always reference the folder of the calling process:

```
/proc/self/cgroup
```

And we can wrap this into a function (thanks to [Henk Langeveld](http://hlangeveld.nl) from [StackOverflow](http://stackoverflow.com/questions/23513045)):

```
running_in_docker() {
awk -F/ '$2 == "docker"' /proc/self/cgroup | read
}
```

## More

- [http://man7.org/linux/man-pages/man5/proc.5.html](http://man7.org/linux/man-pages/man5/proc.5.html)
- [http://en.wikipedia.org/wiki/Procfs](http://en.wikipedia.org/wiki/Procfs)
- [http://stackoverflow.com/questions/23513045](http://stackoverflow.com/questions/23513045)

**Best regards,** Thomas.
