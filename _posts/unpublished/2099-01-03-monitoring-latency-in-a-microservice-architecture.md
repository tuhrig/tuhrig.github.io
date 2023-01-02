---
layout: post
title: "Monitoring latency in a Microservice Architecture"
date: "2099-01-03"
categories:
- "academic"
- "job" 
- "java"
- "spring"
---

Last year, I helped our working student [Steffen Scheller](https://www.xing.com/profile/Steffen_Scheller7) along his way to his [Bachelor thesis](/assets/pdf/Bachelor-Thesis_Steffen-Scheller.pdf).
Steffen was part of [Bringmeister](https://www.bringmeister.de/) for about a year when he decided to write his final academic paper with us.
Together, we came up with an interesting topic: 

> Monitoring latency in a Microservice Architecture

## Background

At Bringmeister, all software we developed during the last 5 years is composed of microservices.
Information drops into the system at one point, moves from service to service and eventually reaches its goal.

A typical dataflow would look like this:

```
 ------------------------- direction of data flow ----------------------->

 +-----+
 | ERP | -- Master Data ----v
 +-----+            +-------------+        +--------------+    
                    | Aggregation | -----> | Search Index | 
                    |   Service   |        |    Service   | 
                    +-------------+        +--------------+ 
 +-----+                     ^                    |
 | PIM | -- Product Data ----+                    v
 +-----+                                   +-------------+     +----------+
                                           | SaaS Search | --> | Web Shop |
                                           |    Index    |     +----------+  
                                           +-------------+
```

This brings up a couple of questions:

- How long does it take for data (e.g. from `ERP`) to arrive in the search index?
- Which service takes the most time?
- How does a change to the system affect the time? 

Steffen's Bachelor thesis tries to give an answer to those questions. 

## Measuring latency

Steffen implemented a microservice (called _Chronos_) to measure the time between two points in the system (_ERP_ and _search index_).
The service received all incoming data simultaneously to the regular system.
Based on a sampling algorithm it decided which request should be taken for measurement:

- A fixed list of IDs
- Every tenth or hundredth request
- ...

The service then measured the time until the data arrives in the search index.
It did so by actively polling the search index until it returned the expected data.

This approach gave us a couple of advantages:

- We did not relay on logs.
Logs can be late (latency until they arrive in the logging system) or misleading (e.g. written before or way after an operation).
- We did not relay on events from the monitored systems themselves.
For example an event that states that the data was written to the search index, does not mean that the data is already indexed and live, too.
- We had the opportunity to do a lot of customizing. 
For example, we implemented a CSV download of the results but also a [Micrometer](https://micrometer.io/)-based metric for [DataDog](https://www.datadoghq.com/).
- We could easily change the measuring or parts of the system taken into account. 
For example, we compared two different search engines in parallel under production load.

## The results

Steffen's work brought up a lot of interesting results for us.

- The latency from end to end is between 10 and 15 seconds most of the time.
So an update from ERP is live in the shop in about 15 seconds.
- The latency does depend on the load, but not as much as we thought.
Random peaks and outliers are actually bigger as the effect of load.
- Single systems have a huge impact. 
The search index for example is responsible for most of the time needed to get an update live.
So switching the search engine (SaaS) has a huge impact.

![](/images/2023/01/microservice-latency.png)

## Thank you

I would like to thank Steffen for his great thesis and work. 
Congratulations on your Bachelor degree!

## Download

You can download Steffen's Bachelor thesis right [here](/assets/pdf/Bachelor-Thesis_Steffen-Scheller.pdf).
It's written in German.

**Best regards,** Thomas.
