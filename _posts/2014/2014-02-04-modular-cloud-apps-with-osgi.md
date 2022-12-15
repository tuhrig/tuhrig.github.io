---
layout: post
title: "Review: Modular Cloud Apps with OSGi"
date: "2014-02-04"
categories: 
  - "academic"
tags: 
  - "book"
  - "cloud"
  - "java"
  - "osgi"
  - "review"
---

Currently, I am doing my literature studies for my master thesis about OSGi and PaaS. One of the first books I read was [Modular Cloud Apps with OSGi](http://shop.oreilly.com/product/0636920028086.do) from Paul Baker and Bert Ertman published by [O'Reilly](http://www.oreilly.com). Here is what I think about it. Maybe it is a little bit ironic sometimes, but I hope you get the point.

[![IMG_20140204_112527](images/IMG_20140204_112527-230x300.jpg)](http://tuhrig.de/wp-content/uploads/2014/02/IMG_20140204_112527.jpg)

# Far more OSGi than Cloud

_Modular Cloud Apps with OSGi_ is much more over OSGi than cloud. You will read about bundles, [Bndtools](http://bndtools.org), plugins and all the other (standard) OSGi stuff all over the book. The terms IaaS, PaaS and SaaS are explained on page 152 of 190 (including references and index). All stuff before is just OSGi. The only real cloud technology in the book is [Amazon's AutoScaling](http://aws.amazon.com/en/autoscaling)...

# Deployment

As I said, the only "cloud" chapter in the whole book is the one about deployment (~8 pages). The author explains how you can deploy nodes with [Amazon's AutoScaling](http://aws.amazon.com/en/autoscaling) and the [Apache ACE](https://ace.apache.org) provisioning server. An examples as well as some scripts are provided. And by the way, Paul Bakker, the author of the book is a contributer of Apache ACE, which brings us to another topic called _Amdatu_.

# Amdatu

You will find one term over and over in the book: _Amdatu_. [Amdatu](http://www.amdatu.org) is a Java OSGi framework to build (modular) cloud applications. And guess what? It is build by one of the authors of the book, Paul Bakker! This could be the reason why Amdatu seams to be the answer for everything. How should we implement authentication? With the Amdatu token provider! And what about a REST API? Yeah, Amdatu has all the libraries packed in some OSGi bundles! And ever thought about some documentation? Amdatu has already repacked the [Swagger framework](https://helloreverb.com/developers/swagger)! And don't forget Amdatu Social, Amdatu Mongo and all the other stuff reinventing the wheel for you while talking about modularity to help you to don't reinvent the wheel. Also consider to not use standard frameworks like Spring, Amdatu has a more modular solution! But try to [search Amdatu on Google Trends](http://www.google.com/trends/explore#q=Amdatu)...

# The world is not ready for OSGi

The world is still not ready for OSGi! After reading the book you get a notion that every existing Java framework is not modular enough and the only solution is to repack them all. None shall pass.

# Riding the cloud wave

The book is really good to learn something about OSGi and maybe to get some insights into SOA. You will learn what bundles are, how provisioning can be done and what REST is. But the cloud part is really weak. The book does not cover any cloud system, any cloud service or any cloud SDK/API. What a pity. _Modular Apps with OSGi_ would have been a much better title, but cloud sells.

* * *

**Edit**

By the way, there is an excellent [video on YouTube](http://www.youtube.com/watch?v=oN3jYKOQ1Tk) by the authors of the book. IMHO the video explains much better how to build cloud apps with Amdatu and OSGi than the book. Especially the part about Apache ACE becomes very clear. Take a look:

<iframe width="560" height="315" src="//www.youtube.com/embed/oN3jYKOQ1Tk" frameborder="0" allowfullscreen></iframe>

* * *

**Best regards,** Thomas
