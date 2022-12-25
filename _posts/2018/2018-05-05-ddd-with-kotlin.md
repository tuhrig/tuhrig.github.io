---
layout: post
title: "DDD with Kotlin"
date: "2018-05-05"
categories: 
  - "coding"
  - "ddd"
  - "kotlin"
---

I recently made my first steps with [Kotlin](https://kotlinlang.org). 
After exploring the language for a couple of weeks, I tried to translate one of my favorite architectural patterns from my Java background to the Kotlin world - [Domain Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design).

## Example project

I've prepared an [example project on GitHub](https://github.com/bringmeister/ddd-with-kotlin) showing some of the most important concepts of DDD with Kotlin. 
The project implements a fictitious _product service_ which takes care of the bounded context of a product. 
The _product service_ implements domain entities as well as value objects and uses an asynchronous communication via events and commands. 
The example use case (which is described in the [README.md on GitHub](https://github.com/bringmeister/ddd-with-kotlin/blob/master/README.md)) involves communication with five fictitious external systems.

```
+-------------+
| Master Data |
|   Service   |
+-------------+
   |                     << DEMO >>   
   +------------------► +---------+
    1: update product   | Product |
                        | Service |---------+---------+
               +------► +---------+         |         |
      3:       |             |      2:      |         |        5:
 push updates  |             | register new |         | update if master 
               |             |   product    |         | data has changed
   +------------+            |              |         |
   | Media Data | ◄----------+              |         +----------+
   |  Service   |                           |         |          |
   +------------+        4: update if media |         |          |
                          data has changed  |         |          |
                                            ▼         ▼          ▼
                                         +-----+  +------+  +--------+ 
                                         | CDN |  | Shop |  | Search |
                                         +-----+  +------+  +--------+
```

## More

- [https://github.com/bringmeister/ddd-with-kotlin](https://github.com/bringmeister/ddd-with-kotlin)
- [https://kotlinlang.org](https://kotlinlang.org)

**Best regards,** Thomas.
