---
layout: post
title: "Mount Windows folder to Boot2Docker VM"
date: "2015-06-24"
categories: 
  - "devops"
  - "docker"
---

I just stumbled over a post on Stackoverflow ([http://stackoverflow.com/questions/30864466/whats-the-best-way-to-share-files-from-windows-to-boot2docker-vm](http://stackoverflow.com/questions/30864466/whats-the-best-way-to-share-files-from-windows-to-boot2docker-vm)) with the question how to mount a Windows folder to a [Boot2Docker](https://github.com/boot2docker/boot2docker) VM. Although the steps are a little bit confusing, in the end it is not difficult to do.

[![](https://avatars2.githubusercontent.com/u/6394678?v=3)](https://github.com/boot2docker/boot2docker)

# Boot2Docker

[Boot2Docker](https://github.com/boot2docker/boot2docker) is a simple VM to run [Docker](https://www.docker.com/). The VM will run on [VirtualBox](https://www.virtualbox.org/) and Boot2Docker is just a tool to provision this VM (very similar to [Vagrant](https://www.vagrantup.com/), but smaller and customized for using Docker). You simply download and install Boot2Docker and run `boot2docker up` to start the VM. After the VM is up, you can run `boot2docker ssh` to login. Now, we can start to get our Windows folder.

# Mounting the folder

To use one of your Windows folders in your Boot2Docker VM, you need to _mount_ it. To do so, you mount your Windows folder to the VM:

C:/Program Files/Oracle/VirtualBox/VBoxManage sharedfolder add boot2docker-vm -name /opt/my/folder/with/code -hostpath c:/my/folder/with/code

Now you login to your VM via SSH (with `boot2docker ssh`) and do the following:

Make a folder inside your VM:

sudo mkdir /my\_windows\_folder

Mount your stuff from Windows:

sudo mount -t vboxsf /opt/my/folder/with/code /my\_windows\_folder

After that, you can access `c:/my/folder/with/code` inside your Boot2Docker VM:

cd /my\_windows\_folder

Now, that your code is present inside your VM, you can use it with Docker. Either by mounting it as a volume to the container:

docker run -v /my\_windows\_folder:/folder/in/container some/image

Or by using it while building your Docker image:

...
ADD /my\_windows\_folder /folder
...

**Best regards,** Thomas
