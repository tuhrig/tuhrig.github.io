---
layout: post
title: "Flatten a Docker container or image"
date: "2014-03-31"
categories: 
  - "coding"
tags: 
  - "devop"
  - "docker"
  - "linux"
---

Docker containers and respectively images can become fairly large. I recently worked with a Docker image which was over 7 GB big. However, it is pretty easy to flatten an image at the end.

# Difference between save and export

As I described in my last post ([http://tuhrig.de/difference-between-save-and-export-in-docker](http://tuhrig.de/difference-between-save-and-export-in-docker)), there are **two ways to persist a Docker images or container**:

- A Docker _image_ can be _saved_ to a tarball and _loaded_ back again. This will preserve the history of the image.
    
    \# save the image to a tarball
    docker save  > /home/save.tar
    
    # load it back
    docker load < /home/save.tar
    

- A Docker _container_ can be _exported_ to a tarball and _imported_ back again. This will _not_ preserve the history of the container.
    
    \# export the container to a tarball
    docker export > /home/export.tar
    
    # import it back
    cat /home/export.tar | docker import - some-name:latest 
    

# No history

We can use this mechanism to flatten and shrink a Docker container. If we save an image to the disk, its whole history will be preserved, but if we export a container, its history gets lost and the resulting tarball will be much smaller.

We can see the history of a image be running `docker tag <LAYER ID> <IMAGE NAMEgt;`:

vagrant@ubuntu-13:~$ sudo docker images --tree
├─f502877df6a1 Virtual Size: 2.489 MB Tags: busybox-1-export:latest
└─511136ea3c5a Virtual Size: 0 B
  └─bf747efa0e2f Virtual Size: 0 B
    └─48e5f45168b9 Virtual Size: 2.489 MB
      └─769b9341d937 Virtual Size: 2.489 MB
        └─227516d93162 Virtual Size: 2.489 MB Tags: busybox-1:latest

So if we export a container (either an already running one or just start a new one from an image) it will lose its history and all previous layers. This will make it impossible to make a rollback to a certain layer, but it will also shrink the image. My >7 GB image is now >3 GB large, which saves more than 50% of disk space.

# Flatten a Docker container

So it is only possible to "flatten" a Docker container, not an image. So we need to start a container from an image first. Then we can export and import the container in one line:

docker export | docker import - some-image-name:latest 

# What else?

You can use some common Linux tricks to shrink Docker images. One simple trick is to clear the cache of the package manager. So depending on which base image you use you can do something like this (for an Ubuntu/Debian system, for more see [here](http://unix.stackexchange.com/questions/75932/what-stuff-can-be-safely-removed-for-disk-space-sake)):

\# clean apt cache
apt-get clean

# Resources

- [http://stackoverflow.com/questions/22713551/how-to-flatten-a-docker-image](http://stackoverflow.com/questions/22713551/how-to-flatten-a-docker-image)
- [http://docs.docker.io/en/latest/reference/commandline/cli](http://docs.docker.io/en/latest/reference/commandline/cli)
- [http://unix.stackexchange.com/questions/75932/what-stuff-can-be-safely-removed-for-disk-space-sake](http://unix.stackexchange.com/questions/75932/what-stuff-can-be-safely-removed-for-disk-space-sake)

**Best regards,** Thomas
