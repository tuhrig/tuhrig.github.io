---
layout: post
title: "3 ways of installing Oracle XE 11g on Ubuntu"
date: "2014-03-22"
categories: 
  - "academic"
  - "coding"
tags: 
  - "automation"
  - "database"
  - "docker"
  - "linux"
  - "oracle"
  - "puppet"
  - "vagrant"
---

During the last few days I struggled with installing [Oracle XE 11g](http://www.oracle.com/technetwork/database/database-technologies/express-edition/overview/index.html) on an Ubuntu VM. And with "the last few days" I mean "the last few weeks". Sad, but true. Here is what I learned about installing Oracle XE 11g on Ubuntu. But please note that I am not a Linux specialist nor an Oracle specialist - it was my first try. And as you will see, I try to make my life as easy as possible. But let's start with the very basics.

# About Oracle XE 11g

Oracle Database Express Edition 11g (or just _Oracle XE_) is the free version of Oracles 11g database. It was released in 2011 and is the second free version of Oracle's database. The first free version was Oracles XE 10g, which was released in 2005. The latest _paid_ version of Oracle's database is 12c, which was released in 2013.

What is really confusing is the fact that the release date of the paid versions is different from the release date of the free versions. So don' be confused, the latest free database from Oracle is 11g.

| Version | Paid | Free |
| --- | --- | --- |
| Version 9i: | 2001 | \- |
| Version 10g: | 2003 | 2005 |
| Version 11g: | 2007 | 2011 |
| Version 12c: | 2013 | \- |

By the way, the _i_, _g_ and _c_ in the database names stand for _internet_, _grid_ and _cloud_.

You can download Oracle XE from the link below - and this is where the pain begins. First of all you need an Oracle account, but this is free and easy. Then you have the choice between two packages:

- One package for Windows which only runs on a 32-bit machine as Oracle says. But don't worry, it also runs on a 64-bit machine (like mine) and although the unzipped installation folder is called `DISK1` there is no `DISK2` or something. Just run the `setup.exe`.
- One package for Linux which is meant to run on a 64-bit machine as Oracle says. And here is the first pain: The package is only available as [RPM](http://en.wikipedia.org/wiki/RPM_Package_Manager) so you first have to [run alien on it](https://help.ubuntu.com/community/RPM/AlienHowto) to convert it for Ubuntu.

# #1 - Installing Oracle XE by hand

[![b7e](images/b7e-300x217.jpg)](http://tuhrig.de/wp-content/uploads/2014/03/b7e.jpg)

My first approach was to install Oracle XE by hand. Although this seems to be the straightforward solution, it was the most painful one. You have to convert the RPM package to a DEP package, create a `chkconfig` script, create some mystic kernel parameters in a `60-oracle.conf`, set your swap space to 2GB or more, create some more folders, install the DEB package, configure the database, export some environment variables like `ORACLE_HOME` (_are you still with me?_), reload that changes and then start the Oracle service and connect via SQLPlus. Easy, isn't it? That's why Oracle is making so much money with consulting :D

And how to know all this steps? Well, use Google, because the best descriptions I found are not from Oracle. To be precise, I found _no official description_ how to setup the Oracle XE database. Here is what I found on blogs and forums. Just choose one that suits you best. They are all really good, thanks to the people who wrote this!

- [https://community.oracle.com/thread/2229554](https://community.oracle.com/thread/2229554)
- [http://meandmyubuntulinux.blogspot.de/2012/05/installing-oracle-11g-r2-express.html](http://meandmyubuntulinux.blogspot.de/2012/05/installing-oracle-11g-r2-express.html)
- [http://sysadminnotebook.blogspot.de/2012/10/installing-oracle-11g-r2-express.html](http://sysadminnotebook.blogspot.de/2012/10/installing-oracle-11g-r2-express.html)

# #2 - Installing Oracle XE with Vagrant and Puppet

[Vagrant](http://www.vagrantup.com) is a free tool to create Linux VMs automatically. This means you can run Vagrant with a simple configuration file (called `Vagrantfile`) which describes a Linux VM (e.g. how many CPUs it should have or which image should be installed). Vagrant will create a VM according to this configuration and start it. This gives you the ability to create the same machine with the exact same configuration over and over again.

[Puppet](http://puppetlabs.com/) is a tool to orchestrate Linux machines. This means Puppet can automatically install programs, create folders or write files. You can download and use it for free. It perfectly integrates into Vagrant. So you can create a VM with Vagrant and as soon as it is ready it can be orchestrated by Puppet.

The really great thing about Vagrant and Puppet is that such scripts can be exchanged. This means that you can write a setup of scripts for something (e.g. to install Oracle XE) and share it with other people. And this is what those to guys did:

- [Matthew Baldwin](https://github.com/matthewbaldwin) wrote a complete Puppet (and Vagrant) setup to install **Oracle XE on CentOS** (a "_RPM-Linux_"). You find it [here](https://github.com/matthewbaldwin/vagrant-xe11g) on GitHub.
- [Hilverd Reker](http://www.hilverd.com/) wrote a complete Puppet (and Vagrant) setup to install **Oracle XE on Ubuntu 12.04** (a "_DEB-Linux_"). You find it [here](https://github.com/hilverd/vagrant-ubuntu-oracle-xe) on GitHub.

Both projects work the same:

1. Install [Vagrant](http://www.vagrantup.com), [VirtualBox](https://www.virtualbox.org/) and [Puppet](http://puppetlabs.com/)
2. Checkout the GitHub repository or download it as a ZIP-file
3. [Download](http://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html) the Oracle XE installation files and put it in some folder which you can read in the README.md of the projects
4. Go to the root folder of the project and run `vagrant up`. This will download a Linux image, install it to a VM and do all the rest (including installing Oracle XE). Note that this will take some time depending on your internet connection!
5. Now you can call `vagrant ssh` and you got a running Linux VM with Oracle XE!

Both projects work really well and install an Oracle XE instance in a couple of minutes.

# #3 - Installing Oracle XE with Docker

[![homepage-docker-logo](images/homepage-docker-logo-300x248.png)](http://tuhrig.de/wp-content/uploads/2014/03/homepage-docker-logo.png)

This one is the nicest way to install Oracle XE. [Docker](https://www.docker.io/) is an application container for Linux. It is based on [LXC](https://linuxcontainers.org/) and gives you the ability to package complete application including their dependencies to a self-containing file (called an _image_). These images can be exchanged and run on every Linux machine where Docker is installed! Awesome!

Docker images are also shared around the community on [https://index.docker.io](https://index.docker.io). And this is where this two guys come into play:

- [Wei-Ming Wu](https://github.com/wnameless) made a Docker image containing Oracle XE. You can find it [here](https://index.docker.io/u/wnameless/oracle-xe-11g/).
- [Alexei Ledenev](https://github.com/alexei-led) extended Wei-Ming Wu's image to also use the Oracle web console (APEX). You can find it [here](https://index.docker.io/u/alexeiled/docker-oracle-xe-11g/).

Both projects work the same:

1. Install Docker on your Linux machine. You can find instructions for that at [http://docs.docker.io/en/latest/installation/ubuntulinux](http://docs.docker.io/en/latest/installation/ubuntulinux). But it is nothing more then this:
    
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
    sudo sh -c "echo deb http://get.docker.io/ubuntu docker main
    > /etc/apt/sources.list.d/docker.list"
    sudo apt-get update
    sudo apt-get install lxc-docker
            
    
2. Pull the image to your machine:
    
    docker pull alexeiled/docker-oracle-xe-11g
    
3. Run the image:
    
    docker run -d -p 49160:22 -p 49161:1521 -p 49162:8080 alexeiled/docker-oracle-xe-11g
    
4. That's it. Absolutely simple.

The great thing is that those images are not as big as a virtual machine. They only contain the actual application and its environment. But they are packed in a way that they can be executed everywhere right out of the box but are still isolated. You can also make changes to the images and create another image containing your changed system.

# Resources

- Download Oracle XE: [http://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html](http://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html)

- Oracle XE installation guide (1): [https://community.oracle.com/thread/2229554](https://community.oracle.com/thread/2229554)

- Oracle XE installation guide (2): [http://meandmyubuntulinux.blogspot.de/2012/05/installing-oracle-11g-r2-express.html](http://meandmyubuntulinux.blogspot.de/2012/05/installing-oracle-11g-r2-express.html)

- Oracle XE installation guide (3): [http://sysadminnotebook.blogspot.de/2012/10/installing-oracle-11g-r2-express.html](http://sysadminnotebook.blogspot.de/2012/10/installing-oracle-11g-r2-express.html)

- Download Puppet: [http://info.puppetlabs.com/download-puppet-open-source](http://info.puppetlabs.com/download-puppet-open-source)

- Download Vagrant: [http://www.vagrantup.com/downloads.html](http://www.vagrantup.com/downloads.html)

- Download VirtualBox: [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

- Installing Docker: [http://docs.docker.io/en/latest/installation/ubuntulinux](http://docs.docker.io/en/latest/installation/ubuntulinux/)

- How Docker works: [https://www.docker.io/the\_whole\_story](https://www.docker.io/the_whole_story)

- Oracle XE Docker image (1): [https://index.docker.io/u/wnameless/oracle-xe-11g](https://index.docker.io/u/wnameless/oracle-xe-11g)

- Oracle XE Docker image (2): [https://index.docker.io/u/alexeiled/docker-oracle-xe-11g/](https://index.docker.io/u/alexeiled/docker-oracle-xe-11g/)

- Oracle XE Vagrant/Puppet setup for Ubuntu: [https://github.com/hilverd/vagrant-ubuntu-oracle-xe](https://github.com/hilverd/vagrant-ubuntu-oracle-xe)

- Oracle XE Vagrant/Puppet setup for CentOS: [https://github.com/matthewbaldwin/vagrant-xe11g](https://github.com/matthewbaldwin/vagrant-xe11g)

**Best regards**, Thomas
