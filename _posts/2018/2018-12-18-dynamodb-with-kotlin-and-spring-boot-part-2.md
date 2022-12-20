---
layout: post
title: "DynamoDB with Kotlin and Spring Boot (Part 2)"
date: "2018-12-18"
categories: 
  - "aws"
  - "database"
  - "kotlin"
---

About a month ago I published [a short article](https://tuhrig.de/dynamodb-with-kotlin-and-spring-boot) about [Kotlin](https://kotlinlang.org), [Spring Boot](https://spring.io/projects/spring-boot) and [DynamoDB](https://aws.amazon.com/dynamodb).

The post shows a basic setup for DynamoDB with Kotlin and Spring Boot. 
However, it doesn't show a lot of aspects related to typical database implementations such as auditing or migrations. 
To fill this gap, I prepared an [example project](https://github.com/tuhrig/dynamodb-with-kotlin) on GitHub:

> [https://github.com/tuhrig/dynamodb-with-kotlin](https://github.com/tuhrig/dynamodb-with-kotlin)

The project shows various small examples, each highlighting a certain aspect of DynamoDB. 
Every example is implemented as a single executable test (against a DynamoDB running in Docker).

## More

- [https://tuhrig.de/dynamodb-with-kotlin-and-spring-boot](https://tuhrig.de/dynamodb-with-kotlin-and-spring-boot) (part 1)
- [https://github.com/tuhrig/dynamodb-with-kotlin](https://github.com/tuhrig/dynamodb-with-kotlin)

**Best regards,** Thomas.
