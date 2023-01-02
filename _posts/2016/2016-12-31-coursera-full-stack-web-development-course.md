---
layout: post
title: "Coursera Full Stack Web Development Course Review"
date: "2016-12-31"
categories: 
  - "academic"
  - "coding"
  - "angularjs"
  - "javascript"
---

During the last 6 months I did the [Full Stack Web Development](https://www.coursera.org/specializations/full-stack) course on [Coursera](https://www.coursera.org). 
Since I'm currently about to finish the course by implementing my final capstone project ([https://github.com/tuhrig/mebo](https://github.com/tuhrig/mebo)), I wanted to share my thoughts about the course and its pros and cons.

## About the course

The [Full Stack Web Development](https://www.coursera.org/specializations/full-stack) course consists of 6 single courses. 
Altogether, they make up the complete course which Coursera calls a specialization.

1. HTML, CSS and JavaScript (3 weeks)
2. Front-End Web UI Frameworks and Tools (4 weeks)
3. Front-End JavaScript Frameworks: AngularJS (4 weeks)
4. Multiplatform Mobile App Development with Web Technologies (4 weeks)
5. Server-side Development with NodeJS (4 weeks)
6. Full Stack Web Development Specialization Capstone Project (8 weeks)

It's possible to take a single course, but it's recommended to take all of them and do them one after another in the given order.
The complete specialization takes 27 weeks and costs 70 € per course, so 420 € in total.

## Course #1: HTML, CSS and JavaScript

This course thoughts the basics about HTML, CSS and JavaScript. 
It is made for beginners, so if you have a little knowledge about these topics you will not learn much. 
On the other side, if you have absolutely zero knowledge, you will have a hard time to learn everything in just 3 weeks. 
That's how long the course will take.

👎🏼 I don't recommend this one. 
If you know HTML, CSS and Javascript you won't learn anything from the course. 
And if you are absolutely new to those technologies, the course is far too short.

## Course #2: Front-End Web UI Frameworks and Tools

This course focuses on [Bootstrap CSS](http://getbootstrap.com). 
Although [Bootstrap CSS](http://getbootstrap.com) isn't too complicated, a lot of people don't understand the principles behind it (e.g. the grid system with its rows and columns). 
So even if you used Bootstrap CSS before, this course will help you to understand things better.

👍🏼 I recommend this one as it really helps to understand one of the most popular UI frameworks right now.

## Course #3: Front-End JavaScript Frameworks: AngularJS

What the second course was for [Bootstrap CSS](http://getbootstrap.com), this one is for [AngularJS](https://angularjs.org). 
And again, if you know [AngularJS](https://angularjs.org) there will be nothing new to you. 
But if you are new to AngularJS this course will find the right tempo to give you a good first glance. 
However, the course is not really up-to-date with AngularJS' latest version.

👍🏼 I recommend it, as this course gives you a good introduction to AngularJS.

## Course #4: Multiplatform Mobile App Development with Web Technologies

This course is made up around the [Ionic Framework](https://ionicframework.com/) for mobile apps. 
If you are interested in building mobile apps, this course will teach you one out of a thousand possibilities to do it. 
I think the Ionic Framework is not the worst way to make mobile apps, however the course is still very opinionated and has a very small focus.

👎🏼 Out of all 6 courses, I recommend this one at least. 
Ionic might be good for some use cases, but the example app made in the course doesn't benefit from it in any way. 
It's just the very same app as made before. 
Making everything responsive would be a much better approach. 
This course also relies heavily on installed software like Ionic itself and Android or iOS simulators. 
If one of those pieces of software don't run on your device, you are screwed. 
It took me hours to get the Android simulator to run, before I switched to the iOS simulator which also took me hours.

## Course #5: Server-side Development with NodeJS

This course is about [NodeJS](https://nodejs.org) and [MongoDB](https://www.mongodb.com). 
Although the architecture of the example app is terrible (they make database queries in the REST controllers!), the course gives a nice introduction to server-side JavaScript. 
Both technologies - [NodeJS](https://nodejs.org) and [MongoDB](https://www.mongodb.com) - are state of the art.

👍🏼 I can recommend this one to get started with [NodeJS](https://nodejs.org) and MongoDB, but also to see some draw backs of those very hyped technologies.

### Course #6: Full Stack Web Development Specialization Capstone Project

At the end of the course, everybody must implement a final project. 
The project should show the learned skills and should - of course - use technologies thought in the course. 
So you are forced to get your hands dirty and write some own code.

👍🏼 This part of the specialization is very interesting, but has some pitfalls, too. 
It's important to choose the project idea wisely.
The course only takes 8 weeks from which only 2 are dedicated to actual programming. 
So whatever you implement, it must be small.

**Special note:** 
To complete the course, you must deploy your project to [IBM's PaaS Bluemix](https://www.ibm.com/cloud-computing/bluemix) for which you will get a test account. 
As I worked with [AWS](https://aws.amazon.com) and similar technologies before, this wasn't too hard for me to do. 
However, Bluemix is a terrible platform. 
If you are not familiar with PaaS, plan some extra time to get things working. 
The course will not prepare you for that in any way.

## How Coursera works

Before you take a course at [Coursera](https://www.coursera.org), you should first understand how the platform works: 
Courses on Coursera are mode up of online videos, text and PDFs, exercises and assignments. 
It's up to the teacher of the course how it is laid out.

Every course runs regularly at some specific date and will end at some specific date. 
This means a course might start every 2 months beginning at the first of the month and ends 4 weeks later. 
You **must (!)** take the course at this period of time. 
It's just like a physical class you would take at school or university.

Most courses require assignments to complete them. 
This means there will be some exercise at the end you must fulfil and upload the solution. 
Most assignments are peer-graded which means that you must review your classmates and you will be reviewed by them.

At the end, you will get a certificate with a lot of buzzwords for the course.

In my opinion, Coursera is nice, but it's not the same as a real physical class at university. 
Especially the peer-graded assignments are a problem. Some people tend to criticize the most odd things, while others just give you the points without even looking at your work. 
It's completely up to you how serious you take it.

## What I learned

### Things I liked to learn

- Course 2: Learning Bootstrap CSS principles from the scratch
- Course 5: [NodeJS](https://nodejs.org) and [MongoDB](https://www.mongodb.com)
- Course 6: [CloudFoundry](https://www.cloudfoundry.org), [TravisCI](https://travis-ci.org), unit testing for [NodeJS](https://nodejs.org)

### Things I didn't like after learning them

- Course 4: [Ionic](https://ionicframework.com), Android and iOS simulators
- Course 6: [IBM Bluemix](https://www.ibm.com/cloud-computing/bluemix)

## What I missed

What I missed completely during all 6 courses was testing. 
None of the courses teach anything about testing, neither in the front end ([Jasmine](https://jasmine.github.io), [Protractor](http://www.protractortest.org)) nor in the backend ([Mocha](https://mochajs.org), [Sinon](http://sinonjs.org)).

## More

- Course homepage: [https://www.coursera.org/specializations/full-stack](https://www.coursera.org/specializations/full-stack)
- My Capstone Project on [GitHub](https://github.com/tuhrig/mebo)
- Sample app for Bluemix: [https://github.com/IBM-Bluemix/node-helloworld](https://github.com/IBM-Bluemix/node-helloworld)
- My [blog post](/coursera-full-stack-web-development-capstone-project) about my Coursera Capstone Project

**Best regards,** Thomas.
