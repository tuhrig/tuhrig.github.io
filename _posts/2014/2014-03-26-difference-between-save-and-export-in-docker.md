---
layout: post
title: "Difference between save and export in Docker"
date: "2014-03-26"
categories: 
  - "coding"
tags: 
  - "container"
  - "devop"
  - "docker"
  - "linux"
  - "vm"
---

I recently played around with Docker, an application container and virtualization‎ technology for Linux. It was pretty cool and I was able to create Docker images and containers within a couple of minutes. Everything was working right out of the box!

At the end of my day I wanted to persist my work. I stumbled over the Docker commands `save` and `export` and wondered what their difference is. So I went to StackOverflow and asked a [question](http://stackoverflow.com/questions/22655867/what-is-the-difference-between-save-and-export-in-docker) which was nicely answered by [mbarthelemy](http://stackoverflow.com/users/1014586/mbarthelemy). Here is what I found out.

# How Docker works (in a nutshell)

Docker is based on so called _images_. These images are comparable to virtual machine images and contain files, configurations and installed programs. And just like virtual machine images you can start instances of them. A running instance of an image is called _container_. You can make changes to a container (e.g. delete a file), but these changes will not affect the image. However, you can create a new image from a running container (and all it changes) using `docker commit <container-id> <image-name>`.

**Let's make an example:**

\# we pull a base Docker image called busybox
# just like in the official Hello-World-example
sudo docker pull busybox

# let's check which images we have
# we should see the image busybox
sudo docker images

# now we make changes to a container of this image
# in this case we make a new folder
sudo docker run busybox mkdir /home/test

# let's check which containers we now have
# note that the container stops after each command
# we should see a busybox container with our command
sudo docker ps -a

# now we can commit this changed container
# this will create a new image called busybox-1
# you see the with the command above
sudo docker commit busybox-1

# let's check which images we have now
# we should see the image busybox and busybox-1
sudo docker images

# to see the difference between both images we
# can use the following check for folders:
sudo docker run busybox \[ -d /home/test \] && echo 'Directory found' || echo 'Directory not found'
sudo docker run busybox-1 \[ -d /home/test \] && echo 'Directory found' || echo 'Directory not found' 

Now we have two different images (`busybox` and `busybox-1`) and we have a container made from `busybox` which also contains the change (the new folder `/home/test`). Let's see how we can persist our changes.

# Export

Export is used to persist a container (not an image). So we need the container id which we can see like this:

sudo docker ps -a

To export a container we simply do:

sudo docker export > /home/export.tar 

The result is a TAR-file which should be around 2.7 MB big (slightly smaller than the one from `save`).

# Save

Save is used to persist an image (not a container). So we need the image name which we can see like this:

sudo docker images

To save an image we simply do:

sudo docker save busybox-1 > /home/save.tar

The result is a TAR-file which should be around 2.8 MB big (slightly bigger than the one from `export`).

# The difference

Now after we created our TAR-files, let's see what we have. First of all we clean up a little bit - we remove all containers and images we have right now:

\# first we see which containers we have
sudo docker ps -a

# now we remove all of them
sudo docker rm # now we see which images we have
sudo docker images

# and we remove them too
sudo docker rmi busybox-1
sudo docker rmi busybox 

We start with our export we did from the container. We can import it like this:

\# import the exported tar ball:
cat /home/export.tar | sudo docker import - busybox-1-export:latest

# check the available images
sudo docker images

# and check if a new container made from this image
# contains our folder (it does!)
sudo docker run busybox-1-export \[ -d /home/test \] && echo 'Directory found' || echo 'Directory not found'

We can do the same for the saved image:

\# import the exported tar ball:
docker load < /home/save.tar

# check the available images
sudo docker images

# and check if a new container made from this image
# contains our folder (it does!)
sudo docker run busybox-1 \[ -d /home/test \] && echo 'Directory found' || echo 'Directory not found'

**So what's the difference between both?** Well, as we saw the exported version is slightly smaller. That is because it is _flattened_, which means it lost its history and meta-data. We can see this by the following command:

\# shows the layers of all images
sudo docker images --tree

If we run the command we will see an output like the following. As you can see there, the exported-imported image has lost all of its history whereas the saved-loaded image still have its history and layers. This means that you cannot do any rollback to a previous layer if you export-import it while you can still do this if you save-load the whole (complete) image (you can go back to a previous layer by using `docker tag <LAYER ID> <IMAGE NAME>`).

vagrant@ubuntu-13:~$ sudo docker images --tree
├─f502877df6a1 Virtual Size: 2.489 MB Tags: busybox-1-export:latest
└─511136ea3c5a Virtual Size: 0 B
  └─bf747efa0e2f Virtual Size: 0 B
    └─48e5f45168b9 Virtual Size: 2.489 MB
      └─769b9341d937 Virtual Size: 2.489 MB
        └─227516d93162 Virtual Size: 2.489 MB Tags: busybox-1:latest

**Best regards**, Thomas
