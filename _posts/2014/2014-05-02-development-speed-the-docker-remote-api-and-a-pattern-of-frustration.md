---
layout: post
title: "Development speed, the Docker remote API and a pattern of frustration"
date: "2014-05-02"
categories: 
  - "academic"
  - "coding"
  - "devops"
tags: 
  - "cloud"
  - "design-pattern"
  - "docker"
  - "java"
  - "java-8"
---

One of the challenges [Docker](https://www.docker.io) is facing right now, is its own development speed. Since its [initial release](https://github.com/dotcloud/docker/commit/a27b4b8cb8e838d03a99b6d2b30f76bdaf2f9e5d) in January 2013, there have been over 7.000 commits (in one year!) by more than 400 contributors. There are more than 1.800 forks on GitHub and Dockers brings up approximately one new release per month. Docker is in a super fast development right now and this is really great to see!

However, this very high development speed leaves a lot of third-party tools behind. If you develop a tool for Docker, you have to keep a very high pace. If not, your tool is outdated within a month.

# Docker remote API client libraries

A good example how this development speed affects projects, are the remote API libraries for Docker. Docker offers a JSON API to access Docker in a programmatic way. It enables you for example to list all running containers and stop a specific one. All via JSON and HTTP requests.

To use this JSON API in a convenient way, people created bindings for their favorite programming language. As you can see below, there exist bindings for JavaScript, Ruby, Java and many more. **I used some of them on my own and I am really thankful for the great work their developers have done!**

But many of those libraries are outdated at the time I am writing this. To be exact: all of them are outdated! The current remote API version of Docker is v1.11 (see [here](http://docs.docker.io/reference/api/docker_remote_api) for more) which none of the remote API libraries supports right now. Many of them don't even support v1.10 or v1.9.

Here is the list of remote API tools as you find it at [http://docs.docker.io/reference/api/remote\_api\_client\_libraries/](http://docs.docker.io/reference/api/remote_api_client_libraries/).

| Language | Name | Remote API |
| --- | --- | --- |
| Python | [docker-py](https://github.com/dotcloud/docker-py) | v1.9 |
| Ruby | [docker-api](https://github.com/swipely/docker-api) | v1.10 |
| JavaScript (NodeJS) | [dockerode](https://github.com/apocas/dockerode) | v1.10 |
| JavaScript (NodeJS) | [ocker.io](https://github.com/appersonlabs/docker.io) | v1.7 |
| JavaScript (Angular) **WebUI** | [dockerui](https://github.com/crosbymichael/dockerui) | v1.8 |
| Java | [docker-java](https://github.com/kpelykh/docker-java) | v1.8 |
| Erlang | [erldocker](https://github.com/proger/erldocker) | v1.4 |
| Go | [dockerclient](https://github.com/samalba/dockerclient) | v1.10 |
| PHP | [Docker-PHP](http://stage1.github.io/docker-php/) | v1.9 |
| Scala | [reactive-docker](https://github.com/almoehi/reactive-docker) | v1.10 |

# How to deal with rapidly evolving APIs

How to deal with rapidly evolving APIs is a difficult question and IMHO Docker made the right decision. By solely providing a JSON API Docker chose a modern and universal technique. A JSON API can be used in any language or even in a web browser. JSON (together with a RESTful API) is the state-of-the-art technique to interact with services. Docker even leaves the possibility to fall back to an old API version by adding an version identifier to the request. Well done.

But the decision to stay "universal" (by solely providing a JSON API) also means to don't get specific. Getting specific (which means to use Docker in a certain programming language) is left to the developers of third party tools. These tools are also evolving rapidly right now, no matter if those are remote API bindings, deployment tools (like [Deis.io](http://deis.io/)), or hosting solutions (like [CoreOS](https://coreos.com/)). This enriches the Docker ecosystem and makes the project even more interesting.

# Bad third party tools will fall back on you

The problem is, even if Docker made a good job (which they did!), outdated or poorly implemented third party tools will fall back on Docker, too. If you use a third party library (which you maybe found via the official website) and it works fine, you will be happy with Docker **and** the third party library. But if the library doesn't work next month because you updated Docker and the library doesn't take care of the API version, you will be frustrated about the tool **and** about Docker.

# Pattern of frustration

This pattern of frustration occurs a lot in software development. Bad libraries cause frustrations about the tool itself. Let's take Java as an example. A lot of people complain about Java that it is verbose, uses class-explosions as a pattern and makes things much more complicated as they should be. The famous `AbstractSingletonProxyFactoryBean` class of the Spring framework is just such an example (see [+Paul Lewis](https://plus.google.com/+aerotwist/posts/1QhcnQizuPc)). Another example is reading a file in Java which was an awful pain:

try {
    File input = new File("file.txt");
    FileReader fr = new FileReader(input);
    BufferedReader reader = new BufferedReader(fr);
    String line = "";
    StringBuilder builder = new StringBuilder();
    while ((line = reader.readLine()) != null)
        builder.append(line);
    reader.close();
    String content = builder.toString();
} 
catch (FileNotFoundException e) {
    e.printStackTrace();
} 
catch (IOException e) {
    e.printStackTrace();
}

And even the new NIO API which came with Java 7 is not as easy as it could be:

String content = new String(Files.readAllBytes(Paths.get("file.txt")));

You need to put a `String` into a `Path` to pass it into static method which output you need to put into a `String` again. Great idea! But what about something like this:

String content = EasyFileReader.read("file.txt");

However, it is not the fault of Java, but of a poorly implemented third party tool. If you need to put a `File` into a `FileReader` which you need to put into a `BufferedReader` to be able to read a file line by line into a `StringBuilder` you use a terrible I/O library! But anyway, you will be frustrated about Java and how verbose it is (and maybe also about the API itself).

**This pattern applies to many other things:** You are angry about your smartphone, because of a poorly coded app. You are angry about Eclipse because it crashes with a newly installed plugin. And so on...

I hope this pattern of frustration will not apply to Docker and the community will develop a stable ecosystem of tools to provide a solid basis for development and deployment with Docker. A tool like Dockers lives trough its ecosystem. If the tools are buggy or outdated, people will be frustrated about Docker - and that would be a shame, because **Docker is really great!**

**Best regards,** Thomas
