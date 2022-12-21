---
layout: post
title: "Working with GerritHub.io"
date: "2014-01-17"
categories: 
  - "academic"
  - "coding"
  - "programming"
  - "python"
---

This semester I attended an university course called [System Engineering and Management](http://v.hdm-stuttgart.de/studienangebot/vorlesung_detail?vorlid=4800524&sgbvsid=4700833) taught by [Prof. Walter Kriha](http://kriha.de/). Together with [Jan Müller](https://github.com/jaenthemaen), I build a continuous integration environment for the course. As we were looking for a code review system we found a plattform called [GerritHub.io](https://gerrithub.io/) which offered a Gerrit system for [GitHub](https://github.com/) as a service. Here is our experience with this service.

# Gerrit

[![gerrit-logo](images/gerrit-logo.png)](http://tuhrig.de/wp-content/uploads/2014/01/gerrit-logo.png)

[Gerrit](https://code.google.com/p/gerrit/) is an open-source tool to do code reviews with Git. In its core it encapsulates a Git implementation (JGit in this case) and works exactly like Git for a developer. You can clone a Gerrit repository, commit and push changes to it. The only difference is that code _must_ be reviewed by somebody else before it is merged into the real repository. Gerrit acts like a layer between the developer and the repository. It is used to enforce a certain review policy in (big) teams.

# GerritHub.io

## A good idea

[GerritHub.io](https://gerrithub.io/) offers a Gerrit system for GitHub as a service. This is very nice, because setting up Gerrit on our own can be very annoying. Gerrit is written in Java and comes with a Maven pom file. This _should_ make things easy, but it doesn't. Some dependencies needed by Gerrit are not in public Maven repositories so you have to get theses libraries first on our own. If you need a certain plugin for Gerrit (e.g. to connect easily to GitHub) it gets even worse. Most plugins depend on a certain version of Gerrit and mostly not on the current one. The versioning of the plugins and of Gerrit isn't the same, too. Therefore, GerritHub.io is a really good idea.

## Let's begin with something positive, it is easy!

To use [GerritHub.io](https://gerrithub.io/) is really easy. You have to create a developer account and connect this account with our GitHub account. Then you can clone your GitHub repository to GerritHub.io. From this point on you only push and pull to GerritHub.io, not to GitHub any more. GerritHub.io lets you do some settings and will offer you the code review. After a review it will automatically merge the code back to GitHub so both repositories will stay synchronized. It's really easy.

## IMHO, it's very buggy

[![conversation_with_GerritHub](images/conversation_with_GerritHub.png)](http://tuhrig.de/wp-content/uploads/2014/01/conversation_with_GerritHub.png) GerritHub.io is a really good idea and it is really easy to use, but - **and this is my own humbling opinion** - it is really really buggy.

We used GerritHub.io for no longer than two months in a small team of two persons. We pushed maybe 20 commits. In this time we had approximately 5 problems with the platform which we couldn't fix on our own and which blocked use completely. There were no error messages and as far as I understand nothing that we as a user did wrong. So we wrote an email to the support. And another one. And another one...

The support of GerritHub.io was really nice and really fast. But more important, they always fixed our problems. Sometimes it was something with their file system, sometimes a problem with OAuth, sometimes something else. Whatever, this should not happen. I know that GerritHub.io is for free and maybe it is a very new project. But it is backed by a company called [GerritForge.com](www.gerritforge.com) which tries to make money with such products. But this doesn’t cast a positive light on the company. Besides the blocking bugs on GerritHub.io, there were some minor bugs like the search/filter function which didn't find any projects or the user interface which sometimes just stopped loading anything. IMHO, a project should not go public like this.

If GerritHub.io will fix the problems, the platform could be a really great product. And maybe the problems are already fixed when you read this post. But right now, I can not recommend this platform. It's crap.

**Best regards,** Thomas
