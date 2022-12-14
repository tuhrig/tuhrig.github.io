---
layout: post
title: "Layering of Docker images"
date: "2014-05-09"
categories: 
  - "devops"
tags: 
  - "build"
  - "cloud"
  - "docker"
---

Docker images are great! They are not only portable application containers, they are also building blocks for application stacks. Using a Docker registry or the public Docker index, you can compose setups just by downloading the right Docker image.

But Docker images are not only building blocks for applications, they also use a kind of "_build block_" themselves: **layers**. Every Docker image consists of a set of layers which make up the final image.

# Layers

Let us consider the following _Dockerfile_ to build a simple Ubuntu image with an Apache installation:

FROM ubuntu
RUN apt-get update
RUN apt-get install -y apache2
RUN touch /opt/a.txt

If we build the image by calling `docker build -t test/a .` we get an image called `a`, belonging to a repository called `test`. We can see the history of your image by calling `docker history test/a`:

$ docker history test/a
IMAGE               CREATED              CREATED BY                                      SIZE
4dc359259700        About a minute ago   /bin/sh -c touch /opt/a.txt                     8 B
9977b78fbad7        About a minute ago   /bin/sh -c apt-get install -y apache2           54.17 MB
e83b3bf07b42        2 minutes ago        /bin/sh -c apt-get update                       20.67 MB
9cd978db300e        3 months ago         /bin/sh -c #(nop) ADD precise.tar.xz in /       204.4 MB
6170bb7b0ad1        3 months ago         /bin/sh -c #(nop) MAINTAINER Tianon Gravi 

The final image `a` consists of six intermediate images as we can see. The first three layers belongs to the Ubuntu base image and the rest is ours: one layer for every build instruction.

We will see the benefit of this layering if build a slightly different image. Let's consider this Dockerfile to build nearly the same image (only the text file in the last instruction has a different name):

FROM ubuntu
RUN apt-get update
RUN apt-get install -y apache2
RUN touch /opt/b.txt

When we build this file, the first thing we will notice is that the build is much faster. Since we already created intermediate images for the first three instructions (namely `FROM...`, `RUN...` and `RUN...`), Docker will reuse those layers for the new image. Only the last layer will be created from scratch. The history of this image will look like this:

$ docker history test/b
IMAGE               CREATED              CREATED BY                                      SIZE
c0daf4be2ed4        42 seconds ago       /bin/sh -c touch /opt/b.txt                     8 B
9977b78fbad7        About a minute ago   /bin/sh -c apt-get install -y apache2           54.17 MB
e83b3bf07b42        3 minutes ago        /bin/sh -c apt-get update                       20.67 MB
9cd978db300e        3 months ago         /bin/sh -c #(nop) ADD precise.tar.xz in /       204.4 MB
6170bb7b0ad1        3 months ago         /bin/sh -c #(nop) MAINTAINER Tianon Gravi 

As we see, all layers are the same as for image `a`, except of the first one where we `touch` a different file!

# Benefits

Those layers (or intermediate images or whatever you call them) have some benefits. Once we build them, Docker will reuse them for new builds. This makes the builds much faster. This is great for contentious integration, where we want to build an image at the end of each successful build (e.g. in Jenkins). But the build is not only faster, the images are also smaller, since intermediate images are shared between images.

But maybe the best things are rollbacks: since every image contains all of its building steps, we can easily go back to a previous step if we want so. This can be done tagging a certain layer. Let's take a look at image `b` again:

$ docker history test/b
IMAGE               CREATED             CREATED BY                                      SIZE
c0daf4be2ed4        3 hours ago         /bin/sh -c touch /opt/b.txt                     8 B
9977b78fbad7        3 hours ago         /bin/sh -c apt-get install -y apache2           54.17 MB
e83b3bf07b42        3 hours ago         /bin/sh -c apt-get update                       20.67 MB
9cd978db300e        3 months ago        /bin/sh -c #(nop) ADD precise.tar.xz in /       204.4 MB
6170bb7b0ad1        3 months ago        /bin/sh -c #(nop) MAINTAINER Tianon Gravi 

If we want to make a rollback and remove the last layer (maybe the file should be called `c.txt` instead of `b.txt`) we can do so by tagging the layer `9977b78fbad7`:

$ docker tag 9977b test/b

Let's take a look at the new history:

$ docker history test/b
IMAGE               CREATED             CREATED BY                                      SIZE
9977b78fbad7        3 hours ago         /bin/sh -c apt-get install -y apache2           54.17 MB
e83b3bf07b42        3 hours ago         /bin/sh -c apt-get update                       20.67 MB
9cd978db300e        3 months ago        /bin/sh -c #(nop) ADD precise.tar.xz in /       204.4 MB
6170bb7b0ad1        3 months ago        /bin/sh -c #(nop) MAINTAINER Tianon Gravi 

Our last layer is gone and with the layer the text file `b.txt`!

**Best regards,**
Thomas
