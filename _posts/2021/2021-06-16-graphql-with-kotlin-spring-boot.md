---
layout: post
title: "GraphQL with Kotlin + Spring Boot"
date: "2021-06-16"
categories: 
  - "coding"
  - "kotlin"
  - "spring"
---

Our frontend team at [Bringmeister](https://www.bringmeister.de) uses [GraphQL](https://graphql.org) as one of their major tools when build our online shop. 
They're using it to query different APIs and repositories to collect and gather all of their data.

Recently, they showed me a Kotlin library for GraphQL from Netflix:

> [https://netflix.github.io/dgs](https://netflix.github.io/dgs)

Since they have been so enthusiastic about GraphQL I gave it a chance and made a very small Kotlin + Spring Boot app using the library. 
And hey, it was really fun! 
You can find the project right here:

> [https://github.com/tuhrig/gql-with-kotlin](https://github.com/tuhrig/gql-with-kotlin)

The app offers an API in order to request `users`. 
An user consists of typical attributes like `name` and `email`. 
However, it also has a list of `posts`. 
The actual user data and the posts are gathered async from an external API, merged and are displayed as a unified REST-response.

![](/images/2021/06/gql-in-app.png)

**Best regards,** Thomas.
