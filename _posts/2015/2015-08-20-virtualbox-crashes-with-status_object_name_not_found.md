---
layout: post
title: "VirtualBox crashes with STATUS_OBJECT_NAME_NOT_FOUND"
date: "2015-08-20"
categories: 
  - "devops"
  - "docker"
---

As I'm a passionate Windows user (sorry...), I often use [VirtualBox](https://www.virtualbox.org) (with [Vagrant](https://www.vagrantup.com)) to pull up a Linux box to use [Docker](https://www.docker.com) or some other "Linux-only" stuff. Usually, this works really fine, but today my VirtualBox crashed with a `STATUS_OBJECT_NAME_NOT_FOUND` error:

[![MXhqz](images/MXhqz.png)](http://tuhrig.de/wp-content/uploads/2015/08/MXhqz.png)

One of those mystic error where everything worked like a charm yesterday at 6 p.m., but when you start your machine today at 9 a.m. it's broken. Damn.

The solution was (and still is) a small Windows patch you need to install on your machine:

[https://support.microsoft.com/en-us/kb/2628582](https://support.microsoft.com/en-us/kb/2628582)

Install, restart, back at work. I hope this will help you, too.

Best regards, Thomas
