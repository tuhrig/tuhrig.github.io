---
layout: post
title: "Presentation of my master thesis"
date: "2014-07-28"
categories: 
  - "academic"
  - "devops"
tags: 
  - "ci-cd"
  - "cloud"
  - "docker"
  - "hdm"
  - "informatica"
  - "java"
---

Over the last six months, I wrote my master thesis about _porting an enterprise OSGi application to a PaaS_. Last Monday, the 21th Juli 2014, I presented the main results of my thesis to my professor (best greetings to you, [Mr. Goik](https://www.mi.hdm-stuttgart.de/mib/kontakt/team/dozenten/goik)!) and to my colleges (thanks to all of you!) at [Informatica](http://www.informatica.com) in [Stuttgart-Weilimdorf](http://en.wikipedia.org/wiki/Stuttgart-Weilimdorf), Germany (where I had written my thesis based on one of their product information management applications, called [Informatica PIM](http://www.informatica.com/us/products/master-data-management/product-information-management/#fbid=o5Ig0zc4uOb)).

Here are the slides of my presentation.

While my master thesis also covers topics like OSGi, VMs and JEE application servers, the presentation focuses on my final solution of a deployment process for the cloud. Based on [Docker](https://www.docker.com), the complete software stack used for the [Informatica PIM server](http://www.informatica.com/us/products/master-data-management/product-information-management/#fbid=o5Ig0zc4uOb) was packaged into separate, self-contained images. Those images have been stored in a repository and were used to automatically setup cloud instances on [Amazon Web Services](https://aws.amazon.com) (AWS).

The presentation gives answers to the following questions:

- What is cloud computing and what is AWS?
- What are containers and what is Docker?
- How can we deploy containers?

To automate the deployment process of Docker images, I implemented my own little tool called _DeployMan_. It will show up at the end of my slides and I will write about it in a couple of days here. Although there are a lot of tools out there to automate Docker deployments (e.g. [fig](http://www.fig.sh) or [Maestro](https://github.com/toscanini/maestro)), I wanted to do my own experiments and to create a prototype for my thesis.

**Enjoy!**

<iframe src="https://docs.google.com/presentation/embed?id=1WSCPrIEOpCqrTvzmIBNWqVxnCj5DNyyBuZeIk0LACgc&amp;start=false&amp;loop=false&amp; frameborder=" 0"="" width="625" height="500"></iframe>

**Best regards,** Thomas
