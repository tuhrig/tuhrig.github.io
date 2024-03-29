---
layout: post
title: "Don't depend on details - an example"
date: "2018-06-27"
categories: 
  - "coding"
  - "architecture"
  - "books"
  - "design-pattern"
  - "kotlin"
---

I recently read [Clean Architecture](https://www.safaribooksonline.com/library/view/clean-architecture-a/9780134494272/) by [Robert C. Martin](https://github.com/unclebob). 
The book gives a simple advice: don't depend on details! 
For Uncle Bob _details_ are things like the database or message broker. 
Your architecture shouldn't depend on the actual implementation of those details. 
Whether you are using _Oracle_, _MySQL_ or _DynamoDB_, whether it's _ActiveMQ_, _Kinesis_ or an _in-memory Guava event bus_ - your architecture shouldn't be affected.

Don't depend on details has some advantages:

- As you don't depend on the actual implementation, you can change it.
You can decide to use DynamoDB instead of MySQL because you want to migrate to AWS.
- You can test your code more easily. 
As you don't depend on an actual implementation you can use mocks or an in-memory variant for testing and local development.
- You can change your mind. 
Being independent gives you the freedom to change things in the future.

Today I updated one of my [demo projects](https://github.com/tuhrig/ddd-with-kotlin) on GitHub. 
I replaced [Guava's event bus](https://github.com/google/guava/wiki/EventBusExplained) with [Spring's own event bus implementation](https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2). 
Both implementations do exactly the same - they send events. 
However, my code had a strong dependency to Guava. 
Each and every listener imported an annotation from the Guava library. 
A detail on which I depended.

The result was this pull request:

![](/images/2018/06/pr.png)

Although the implementation of Guava and Spring are doing exactly the same, I needed to change 18 files. 
There were no tests and it's just a small and stupid demo project - still, I needed to touch 18 files in order to switch from one in-memory message broker to another. 
This shouldn't be the case! 
Don't depend on details!

## More

- [https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html)

**Best regards,** Thomas.
