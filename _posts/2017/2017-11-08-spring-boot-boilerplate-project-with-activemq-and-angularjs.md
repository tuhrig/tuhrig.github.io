---
layout: post
title: "Spring-Boot boilerplate project with ActiveMQ and AngularJS"
date: "2017-11-08"
categories: 
  - "coding"
  - "angularjs"
  - "spring"
---

During the last couple of months, I wrote a lot of blog posts about [ActiveMQ](http://activemq.apache.org/), [AngularJS](https://angularjs.org/) and [Spring](https://spring.io/). 
Most of those blog posts include some small code snippets, but nothing more. 
That's why I decided to make a boilerplate project to show how all those different techniques can go hand in hand in a bigger example.

## Boilerplate project

You can find the boilerplate project on GitHub:

> [https://github.com/tuhrig/Review_System_Demo](https://github.com/tuhrig/Review_System_Demo)

The project contains three small microservices plus a user interface (which is just another microservice). 
All backend stuff is written in Java (using [Spring Boot](https://projects.spring.io/spring-boot)). 
The frontend is written in AngularJS (version 1.6.X). 
The three microservices communicate with each other via JMS messages (over ActiveMQ). 
The UI uses a [Zuul](https://github.com/Netflix/zuul) proxy in order to use the REST APIs of the three different microservices. 
Everything is build with [Gradle](https://gradle.org/) (for the backend) and [Grunt](https://gruntjs.com/) (for the frontend).

## Use case

The boilerplate project is based on a fictitious example:

- A user can submit a review about a product
- A review consists of a subject, the actual content and a rating (0 to 5)
- Each review is checked if it contains inappropriate language
- Based on that, a review will either be approved or rejected
- Every approved review can be seen in a list

## Microservices

The project consists of three microservices plus the UI:

- Review system
- Checking system
- Statistics system
- User interface

### Review System

The review system takes and persists new reviews. 
It also holds the state of each review (_approved_ or _rejected_) and allows to query all approved reviews.

### Checking System

The checking system will examine the content of each application. 
It will reject reviews containing inappropriate language.

### Statistics System

The statistics system will keep track of a simple statistic on how many reviews have been submitted, approved and rejected.

### User Interface

The user interface allows submitting new reviews and to see all approved reviews. 
It also displays the current overall statistics.

## Interaction

The communication between the UI and the backend services is based on REST. 
A Zuul proxy is used to route the UI requests to the different microservices.

On the other hand, the communication between the backend microservices is based on JMS using ActiveMQ. 
Three different types of events are being exchanged (`REVIEW_SUBMITTED_EVENT`, `REVIEW_APPROVED_EVENT` and `REVIEW_REJECTED_EVENT`) between the system. 
Note that all events are sent to a topic - not directly to a service queue (as described [here](/effective-error-handling-for-activemq-topics)).

![](/images/2017/11/microservices.png)

## Conclusion

The boilerplate project tries to reflect my thoughts on a simple architecture and design. 
I've tried to combine different aspects (such as backend, frontend, testing, messaging, etc.) of a typical application, hoping to present my ideas in a more realistic way.

## More

- An interesting view on boilerplate projects: [The best boilerplate app is the one you code yourself](https://mindthecode.com/the-best-boilerplate-app-is-the-one-you-code-yourself/)

**Best regards,** Thomas.
