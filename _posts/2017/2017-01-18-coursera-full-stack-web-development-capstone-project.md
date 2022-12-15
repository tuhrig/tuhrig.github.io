---
layout: post
title: "Coursera Full Stack Web Development Capstone Project"
date: "2017-01-18"
categories: 
  - "academic"
  - "angularjs"
  - "javascript"
---

A couple of days ago I finished my capstone project for the [Full Stack Web Development](https://www.coursera.org/specializations/full-stack) specialization on [Coursera](https://www.coursera.org). It marks the end of the 6-course specialization about [Bootstrap CSS](http://getbootstrap.com), [AngularJS](https://angularjs.org) and [NodeJS](https://nodejs.org).

## The Assignment

The assignment itself is simple:

> Build a web application with the tools taught in the course ([Bootstrap CSS](http://getbootstrap.com), [AngularJS](https://angularjs.org), [NodeJS](https://nodejs.org), [MongoDB](https://www.mongodb.com)).

Everybody has the free choice what to implement, however it should be a small app as it must be implemented during a short period of time. I decided to implement a message board where users can create boards and post messages (more below).

## The Schedule

The capstone project takes 8 weeks in total, while only 2 or 3 weeks are dedicated to actual coding. Each weeks has about 3 hours of workload. So you can spend approximately 9 hours to coding - **but you will definitely spend more time!**

| Week | Topic | Assignment |
| --- | --- | --- |
| 1 | Ideation | \- |
| 2 | Ideation Report | Report (PDF, 2 pages) about your idea |
| 3 | UI Design and Prototyping | \- |
| 4 | UI Design and Prototyping Report | Report (PDF, 2 pages) with UI mockups |
| 5 | Architecture Design and Software Structure | \- |
| 6 | Architecture Design and Software Structure Report | Report (PDF, 3 pages) with architecture, structure, REST URLs, data model |
| 7 | Project Implementation and Final Report | \- |
| 8 | Final Submission and Report | Report (PDF, 2 pages), code on GitHub, running app on IBM Bluemix |

## The Workload

You will spend most of your time for coding, but there will also be a huge amount of time you will spend on organisational tasks. Those tasks might take a lot of time:

- Making a GitHub project including README.md, .gitignore and so on
- Setup a Travis CI build
- Deploy to IBM Bluemix (read tutorials, write a .cfignore and manifest.yml)
- Do all dependency management
- Install and configure MongoDB
- Manage different configs for you local development and IBM Bluemix
- ...

While all of those tasks are absolutely necessary for every project setup, they can easily take up some hours nevertheless. This melts down your coding time.

## My Project

I called my project **MEBO** which simply stands for **Message Board**. It's a small application where users can create boards and post messages on them. Every who know the link to a board can access it and edit all messages. There's no login or so. You can find the project on GitHub: [https://github.com/tuhrig/mebo](https://github.com/tuhrig/mebo).

[![](images/Screen-Shot-2016-12-31-at-19.35.04-1024x913.png)](http://tuhrig.de/wp-content/uploads/2017/01/Screen-Shot-2016-12-31-at-19.35.04.png)

[![](images/Screen-Shot-2016-12-31-at-19.35.59-1024x914.png)](http://tuhrig.de/wp-content/uploads/2017/01/Screen-Shot-2016-12-31-at-19.35.59.png)

## Lessons Learned

- Make it small! Pf course, everybody wants to make this super fancy application with its own user management, administration view and all kinds of features. But make your life easy and do what's needed to demonstrate your skills.
- Start early with the deployment and integration to IBM Bluemix. No matter how well your project is implemented, if it doesn't run on IBM Bluemix (or anywhere else) where I can see it, you will not pass the assignment. Get things up and running from the beginning.
- Let it looks nice. The first impression is very important. If your project looks nice and your GitHub project has a README.md, your're on the right path.

**Best regards,** Thomas.
