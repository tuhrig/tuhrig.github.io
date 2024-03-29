---
layout: post
title: "Docker vs. Heroku"
date: "2014-05-05"
categories:
  - "devops"
  - "cloud"
  - "docker"
---

![](/images/2014/05/Docker-vs-Heroku.png)

Since a couple of weeks I am working with [Docker](https://www.docker.com/) as an application container for Amazon's [EC2](https://aws.amazon.com/de/ec2/). 
Despite my eternal fight with the Docker registry, I am absolutely amazed about Docker and enjoyed my experience.

But sometimes it is hard to explain what Docker is and what is has to do with all this cloud, PaaS and scalability topic. 
So I thought a bit about some similar concepts between Docker and [Heroku](https://www.heroku.com/) - maybe the most popular PaaS provider right now. 
But let's start with a small disclaimer:

## Disclaimer

Docker and Heroku maybe have similar concepts (as you will see below), but they are two completely different things: 
while Docker is an open source software project, Heroku is a commercial service provider. 
You can download, build and install Docker on your own laptop or participate on its online community. 
On Heroku, you can create a user account, pay some money (maybe) and get a really great service and hosting experience for your applications. 
So obviously, Docker and Heroku are very different things. 
But some of their core concepts have some similarities.

## Docker vs. Heroku

<table style="width: 100%">
  <tr><th>Docker</th><th>Heroku</th></tr>
  <tr><td>Dockerfile</td><td>BuildPack</td></tr>
  <tr><td>Image</td><td>Slug</td></tr>
  <tr><td>Container</td><td>Dyno</td></tr>
  <tr><td>Index</td><td>Add-Ons</td></tr>
  <tr><td>CLI</td><td>CLI</td></tr>
</table>

Docker and Heroku have a lot of similarities, especially in their core concepts. 
This makes Docker an interesting alternative for people who are looking for an alternative for Heroku - maybe on their own infrastructure.

## Dockerfile vs. BuildPack

Docker images can be build with a _Dockerfile_. 
A Dockerfile is a set of commands, e.g. to add files and folders or to install packages. 
It defines how the final image should look like. 
Here is an example of a Dockerfile which installs [memcache](https://memcached.org/) from the [official website](https://www.docker.io/learn/dockerfile/level1/):

```
FROM ubuntu
RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
RUN apt-get update
RUN apt-get install -y memcached
```

Heroku's pendant are so called _BuildPacks_. 
BuildPacks are also a set of scripts which are used to set up the final state of an image. 
Heroku comes with a couple of default BuildPacks such as for Java, Python or the Play! framework. 
But you can also write your own. 
Here's a snippet of the [Heroku BuildPack for Java apps](https://github.com/heroku/heroku-buildpack-java):

```
# install maven with base repository
MAVEN_URL="http://s3.amazonaws.com/heroku-jvm-langpack-java/maven.tar.gz"

if [ ! -d .maven ]; then
  echo -n "-----> Installing Maven 3.0.3..."
  curl --silent --max-time 60 --location $MAVEN_URL | tar xz
  chmod +x .maven/bin/mvn
  echo " done"
fi
```

BTW, there are even projects to enable the usage of Heroku's BuildPacks for Docker images (like [this](http://blog.docker.io/2013/05/heroku-buildpacks-on-docker/)).

## Image vs. Slug

When you run a Dockerfile, it creates a _Docker image_. 
Such an image contains all data, files, dependencies and settings you need for your application. 
You can exchange those images and start them right away on any machine with Docker installed.

When you run a build on Heroku, the BuildPack creates a so called _Slug_. 
Those slugs are "are compressed and pre-packaged copies of your application" as Heroku says. 
Similar to Docker's images, they contain all dependencies and can be deployed and started in a very short time.

## Container vs. Dyno

After starting a Docker image, you have a running _container_ of this image.
You can start an image multiple times, to get multiple isolated container of the same application. 
This enables you to build an image once and start easily multiple instances of it.

Heroku does the very same. 
After you build your app with your BuildPack, you get a slug which you can run on a Dyno. 
Such a dyno is "a lightweight container running a single user-specified command" as Heroku describes it.

Heroku even uses [LXC](https://linuxcontainers.org/) for virtualization of their containers (_dynos_), which is the same technology Docker uses at its core.

## Index vs. Add Ons

Docker images can be shared with the community. 
This is possible by uploading them to [the official Docker index](https://index.docker.io/) or your own registry. 
All images on this index can be downloaded and used by everyone. 
Most of them are documented very well and can be started with a single command. 
This makes it possible to run a lot of applications as building blocks. 
Here's an example how to run [elasticsearch](https://www.elastic.co/de/elasticsearch/):

``` 
docker pull dockerfile/elasticsearch
docker run -d -p 9200:9200 -p 9300:9300 dockerfile/elasticsearch
```

A similar concept applies to Heroku's add-on market. 
You can use (or buy) different pre-configured add-ons for your application (e.g. for [elasticsearch](https://addons.heroku.com/bonsai)). 
This makes it possible to build a complex app with common building blocks - such as Docker is doing it!

So both, Docker's index and Heroku's add-ons, underline a service oriented way of developing applications and reusing components.

## CLI

![](/images/2014/05/CLI.png)

Although the four points mentioned above are the most important concepts of both, Docker and Heroku have one more thing in common: 
both have a powerful command line interface which allows managing containers. 
E.g. you can run `heroku ps` to see all your running slugs or `docker ps` to see all your running containers or you can request the log of a certain container.

## More

- Heroku BuildPacks: [https://devcenter.heroku.com/articles/buildpacks](https://devcenter.heroku.com/articles/buildpacks)
- Heroku Slug: [https://devcenter.heroku.com/articles/slug-compiler](https://devcenter.heroku.com/articles/slug-compiler)
- The Docker story: [https://www.docker.io/the_whole_story/](https://www.docker.io/the_whole_story/)

**Best regards,** Thomas.
