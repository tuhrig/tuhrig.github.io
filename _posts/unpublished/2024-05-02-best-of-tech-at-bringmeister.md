---
layout: post
title: "My tech-takeaways from Bringmeister"
date: "2099-04-22"
categories: 
  - "coding"
  - "java"
  - "spring"
  - "vaadin" 
---

[As I wrote before](/bye-bye-bringmeister), after more than six years, my last day at Bringmeister has finally arrived.
But not only am I leaving, Bringmeister itself closes its doors.
It was an interesting time for me and I learned a lot. 
Here are some takeaways from a tech-perspective.

# What I liked

## Kotlin

I hired at Bringmeister as a Java developer. 
But when I came in on my first day, they told me that we are doing Kotlin only.
I never heard of it before.
But looking back, Kotlin was one of the best decisions we made.
It's expressive, versatile, sexy and not too hard to learn for a Java developer.
I totally recommend it.

## Kinesis

We used Kinesis for inter service communication.
It's the AWS alternative to Kafka.
We used it between dozens of services, and it served us well.

## DynamoDB

DynamoDB is a simple NoSQL database. 
It offers a first and a second key, and you can store data as JSON.
And that's basically all you ever need. 
It doesn't fit perfectly to all problems, but it fits good enough for most of it.
It's quick and easy to set-up.
And the best thing: no SQL needed.

## DataDog

We not only built our software, we also did the monitoring and 24/7-support
DataDog always served us well as our main logging und monitoring platform.
We built alerts with it, dashboards and collected logs and metrics.

## Algolia

Algolia served as the main search-index for Bringmeister.
It was blazing fast and I love the API and the SDK.

## Sailthru

## Microservices

## AWS

## Slack

# What I didn't like

## Version Catalogs

We used a version catalog to easily update the dependencies of our microservices.
The idea was to have everything in one place instead repeated in each service over and over again.
And while this idea might sound good (that's why we did it), the reality is different.
You loose one of the biggest advantages of microservices: their independence. 
The version catalog is always an additional step and updating every service will not get easier.

## Salesforce

## Comarch ERP

## Luigi's Box

# What I'm still not sure about

## Shared Common Libraries

This one is similar to the [version catalog](#version-catalogs) mentioned above - but not as bad.
It totally depends on how you implement it.
If you take care, it can be very useful to avoid copy and pasting.
It's important to make the library as independent as possible.
It should not contain domain models or business logic, but only independent utilities that have a general purpose.

## MS Teams

## Camunda

## Vertical Teams

# More

- 

**Best regards,** Thomas.
