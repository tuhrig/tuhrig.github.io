---
layout: post
title: "Everytime a mock returns a mock a fairy dies"
date: "2015-08-25"
categories: 
  - "coding"
tags: 
  - "design-pattern"
  - "java"
  - "java-8"
  - "mockito"
  - "testing"
---

As I was reading about some [Mockito](https://github.com/mockito/mockito) features last week, I stumbled over the following tweet, which really made me laugh:

<blockquote class="twitter-tweet" lang="de"><p lang="en" dir="ltr">Everytime a mock returns a mock a fairy dies</p>— Damian Guy (@damianguy) <a href="https://twitter.com/damianguy/status/4977743956">18. Oktober 2009</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

I actually found the reference to the tweet in the Mockito [source code](https://github.com/mockito/mockito/blob/master/src/main/java/org/mockito/Mockito.java) about "deep mocking" and it's worth to think about it for a minute...

# Deep mocking

Deep mocking is a really great feature from Mockito. It lets a mock return a mock, which will return a mock, which will return a mock, which will... I think you got it. Here's an example how it works:

Builder builder = Mockito.mock(Builder.class, Mockito.RETURNS\_DEEP\_STUBS);

Mockito.when(
    builder.newObject()
           .withId(1)
           .withName("My Object")
           .withDescription("Some description")
           .build()
).thenReturn(anObject);

// Use the mock...
someService.someServiceMethod(builder);

By passing the `Mockito.RETURNS_DEEP_STUBS` argument to Mockito, we tell Mockito to make a deep mock. This makes it possible to mock the result of a complete method chain in the next step.

# Use deep mocking for fluent APIs

Deep mocking is very elegant way to mock [fluent APIs/interfaces](https://en.wikipedia.org/wiki/Fluent_interface) which are very popular right now. Fluent APIs are described by Martin Fowler ([here](http://www.martinfowler.com/bliki/FluentInterface.html)) and are often used for builders, factories, queries or configurations.

While method-call chaining is a really bad way to write code as it's hard to read, hard to debug and hard maintain, fluent APIs are very handy to avoid repetition in the code (e.g. to write something like `builder.setId(1); builder.setName("My Object"); ...`) and to lead the developer through a certain process (e.g. an object creation). Since Java's [stream API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html) they are also part of the core Java language. You will find them in many other places, too:

- Java 8 streams: `list.stream().filter(...).map(...).sum()`
- Mockito: `when(...).thenReturn(...)`
- Apache HTTP Client: `Request.GET(...).connectTimeout(...).execute().returnContent()`
- Spring MockMVC: `mockMvc.perform(get(...).accept(...)).andExpect(status().isOk())`

# Don't use deep mocking for call chains

This brings me back to the tweet about the dying fairies on the top of this post. While the tweet doesn't apply fluent APIs (IMHO), it definitely applies to bad written code. When every a mock returns a mock (and so on...) some encapsulation is broken. As you need to know details about the internal behavior of the mocked object (to mock it again), you are violating the [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) (the _principle of least knowledge_).

# Fluent APIs vs. Code Chains

It's important to know the difference between a smart and handy fluent API (which you might mock!) and a bunch of ugly code chains (which you better refactor!). Code chains break encapsulations and leak the internals of an object to the outer world. Fluent APIs on the other provide a small DSL ([domain specific language](https://en.wikipedia.org/wiki/Domain-specific_language)) which doesn't leek internals as it only returns the "next possible step" and guides you on your way.

# References

- [http://blogs.atlassian.com/2013/02/mockito-makes-mocking-fluid-interfaces-easy](http://blogs.atlassian.com/2013/02/mockito-makes-mocking-fluid-interfaces-easy)
- [http://www.martinfowler.com/bliki/FluentInterface.html](http://www.martinfowler.com/bliki/FluentInterface.html)
- [http://mockito.googlecode.com/hg-history/1.8.3/javadoc/org/mockito/Mockito.html#RETURNS\_DEEP\_STUBS](http://mockito.googlecode.com/hg-history/1.8.3/javadoc/org/mockito/Mockito.html#RETURNS_DEEP_STUBS)

**Best regards,** Thomas
