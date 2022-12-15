---
layout: post
title: "Anti Patterns"
date: "2015-02-07"
categories: 
  - "academic"
  - "coding"
tags: 
  - "design-pattern"
  - "java"
---

A couple of days ago I have been to a tech talk from [eBay](www.ebay.com) in Berlin. The talk was hold by a developer of the [eBay Kleinanzeigen](http://kleinanzeigen.ebay.de/) team (a subsection of eBay in Germany) who talked about development patterns his team learned during the years. He mentioned **behavioral patterns**, **design patterns** and **coding pattern** his colleagues and he discovered as good practices for their projects. Since nearly everything could be seen as a pattern, the talk was a mix of learned lessons and actual advises on how to structure code. **Cool!** It is always good to talk and hear about coding experiences and it also reminded me at something which is often more important than patterns: **anti patterns**!

[![2015-01-22 19.27.11](images/2015-01-22-19.27.11-300x225.jpg)](http://tuhrig.de/wp-content/uploads/2015/01/2015-01-22-19.27.11.jpg)

The idea of _anti patterns_ always comes to my mind when I'm reading some old code. And it comes more often if the code is super sophisticated and fancy. The more people try to engineer their code, the more they build things which looks like some complicated pattern but are actually anti patterns.

You like some examples? Here's a very trivial anti pattern, not a big thing. It's just **a bad habit**. It looks like that:

public class MyFactory {
    public MyObject createMyObject() { /\* ... \*/ }
    private MyFactory() { /\* ... \*/ }
}

Anything wrong here? No, just a factory. Anything useless? Yes, a little bit. It's very likely to see a class with only static methods (like a factory) to have a private constructor. Why? Because the developer thought it's evil to instantiate it and he must prevent it for the safety of everybody in the room! So he creates something which look like a singleton (but isn't). He does this in every suitable class, because it's his patterns. But it is actually nothing. What would happen if you instantiate such an object? Nothing. And if you call static methods on that object? Nothing, too. It doesn't look good, but it would work and your IDE would warn you. So what problem does it solve? There's none. It just looks like some useful pattern, but actually does nothing but clutter your code. An anti pattern.

There are more of those small bad habits (like using `final` everywhere) but also bigger ones (like an interface for everything). I'm going to think about this a little more and come up with another example!

**Best regards,** Thomas
