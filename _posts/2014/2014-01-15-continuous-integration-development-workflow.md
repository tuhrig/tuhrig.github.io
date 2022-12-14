---
layout: post
title: "Continuous Integration Development Workflow"
date: "2014-01-15"
categories: 
  - "academic"
  - "coding"
tags: 
  - "ci-cd"
  - "hdm"
  - "programming"
  - "python"
  - "testing"
  - "video"
  - "web"
---

This semester I attended an university course called [System Engineering and Management](http://v.hdm-stuttgart.de/studienangebot/vorlesung_detail?vorlid=4800524&sgbvsid=4700833) taught by [Prof. Walter Kriha](http://kriha.de/). The course has a slightly different topic every year and is made up of presentations from students, research assistants and other lecturers. The topic in this year was continuous integration and software deployment. Together with [Jan Müller](https://github.com/jaenthemaen), I set up a continuous integration environment to develop a Python web app.

# Our demo app

We made a simple demo app to demonstrate your workflow and to show the purpose of the components in our CI environment. We build our app with [Python](http://www.python.org/) and [Flask](http://flask.pocoo.org/) on the server, a simple template rendering for the client and a [MongoDB](http://www.mongodb.org/) at the back-end. The app itself was a simple CRUD application to add, update and delete users from a list.

# The CI environment

[![ci_env_2](images/ci_env_21-1024x577.png)](http://tuhrig.de/wp-content/uploads/2014/01/ci_env_21-e1390945768100.png)

## GitHub

We used GitHub as our central repository and to communicate with the rest of the world. The idea (for a real world team) was to use GitHub as a repository, as a wiki, as an issue tracker and as a community tool, e.g. for pull requests. However, we did not push any code directly to GitHub! Here is why:

## GerritHub

We used GerritHub as a review tool and as our central repository for all development tasks. To do this, GerritHub cloned the original repository from GitHub and automatically synchronized both of them. As a developer we checked out code from GerritHub (not from GitHub!) and also pushed our changes to GerritHub (and, guess what, not to GitHub directly!). After a new change was pushed to GerritHub, all developers of the project received an email notification about the change. The change itself had to be reviewed and approved in GerritHub by at least one other developer. Only after a change was reviewed and approved it went into our code base on GitHub (GerritHub merged it into the code based). This enforced a certain policy in our development process where every piece of code must be reviewed by somebody else.

## BuildBot

After the code was pushed to GerritHub, approved there and merged into GitHub, it was time for our build system. We set up a so called post commit hook in GitHub, which informed our build system about code changes. So each time new code arrived from GerritHub in our GitHub repository our build system started a new build. Since our app was made in Python we decided to use [BuildBot](http://buildbot.net/) (which itself is made in Python) as our build system. However, what we did is possible with any other build system, too. BuildBot did a set of tasks for us:

1. Check out the latest code from GitHub
2. Execute all unit tests
3. Run static code analysis such as PEP8
4. Build an artifact to download
5. Deploy the app to our demo machine

## Demo machine

We set up a demo machine where our BuildBot deployed ever (successful) build. With this automated deployment we always had an up-to-date and running instance of our app. This is great as a team since everybody of the team can see the latest features and show them to potential customers.

# Demo videos

I made a short (~10 minutes) video to demonstrate the development workflow in this environment. The video covers a full development cycle of a new feature from the first check-out of the project to the final result on the demo machine. I made a voice-over in English and in German, both should contain the same (more or less). The video is cut to make it as short as possible, so you won't have to wait for my Eclipse to start...

What I do in the video is the following:

1. Check out the project and run make sure it is in a stable state
2. Implement a new feature in a test-driven way
3. Push the new feature
4. Watch BuildBot doing its work
5. Enjoy the final result on our demo machine

## Demo Video (English): CI Development Workflow

**Video**: [http://www.youtube.com/watch?v=ZYiwhvRDRak](http://www.youtube.com/watch?v=ZYiwhvRDRak)

<iframe width="560" height="315" src="//www.youtube.com/embed/ZYiwhvRDRak" frameborder="0" allowfullscreen></iframe>

## Demo Video (German): CI Development Workflow

**Video**: [http://www.youtube.com/watch?v=tza03NG1xwo](http://www.youtube.com/watch?v=tza03NG1xwo)

<iframe width="560" height="315" src="//www.youtube.com/embed/tza03NG1xwo" frameborder="0" allowfullscreen></iframe>

**Best regards,** Thomas
