---
layout: post
title: "Cloud vendors with Windows"
date: "2014-05-13"
categories: 
  - "devops"
tags: 
  - "cloud"
  - "linux"
  - "windows"
---

The cloud is build on Linux - that is my own humbling opinion. But is it really? To answer this question for myself, I took a look at a bunch of cloud vendors to see what they got under the hood. Here is what I found.

But note that the list is neither complete nor representative. I am also comparing two very different things: _IaaS_ and _PaaS_. While IaaS vendors like AWS provide virtual machines, PaaS vendors like Heroku provide a tooling to setup complete environments.

However, the list shows that most of the vendors use Linux as their base system and the more you go to the PaaS direction, the more Windows vanishes.

  
| Vendor | Windows | Linux | Type | Comment |
| --- | --- | --- | --- | --- |
| [Microsoft Azure](http://azure.microsoft.com/) | yes | yes | IaaS |  |
| [AWS](http://aws.amazon.com/) | yes | yes | IaaS | AWS has a lot of Linux distributions and Windows version on their IaaS EC2. |
| [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/) | yes | yes | IaaS |  |
| [eNlight Cloud](http://www.esds.co.in/) | yes | yes |  | CentOS, Red Hat Enterprise Linux, SUSE Linux, Oracle Linux, Ubuntu, Fedora, Debian, Window Server 2003, Windows Server 2008, Windows 7. |
| [Google App Engine](https://appengine.google.com/) |  |  | PaaS | Google App Engine has a sandbox and hides the OS. |
| [Google Compute Engine](https://cloud.google.com/products/compute-engine/) | yes | yes | IaaS | Linux, FreeBSD, Microsoft Windows |
| [Heroku](https://www.heroku.com/) |  | yes | PaaS | Ubuntu |
| [Jelastic](http://jelastic.com/) |  | yes | PaaS |  |
| [HP Cloud](http://www.hpcloud.com/) |  | yes | IaaS | Based on OpenStack. |
| [OpenShift](https://www.openshift.com/) |  | yes | PaaS | Red Hat Enterprise Linux |
| [Engine Yard](https://www.engineyard.com/) |  | yes | PaaS | Ubuntu, Gentoo |
| [Rackspace](http://www.rackspace.com/) | yes | yes |  |  |
| [Cloud Foundry](http://www.gopivotal.com/platform-as-a-service/pivotal-cf) |  | yes | PaaS |  |

**Best regards,** Thomas
