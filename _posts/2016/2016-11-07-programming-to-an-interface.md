---
layout: post
title: "Programming to an interface"
date: "2016-11-07"
categories: 
  - "coding"
tags: 
  - "architecture"
  - "design-pattern"
  - "gof"
  - "interface"
  - "java"
---

\[caption width="640" align="aligncenter"\]![Interface Message Processor Front Panel](images/640px-Interface_Message_Processor_Front_Panel.jpg)There are many types of interfaces\[/caption\]

> Program to an interface, not an implementation.

This short sentence, introduced in 1994 by the GoF, is one of the most important design principles in software development. However, I have seen several projects where this principle has gone wrong. Here's why:

# Interface vs. Interface

If you come from a Java background like myself and you hear the word _interface_ you will typically think of something like that:

public interface PersonStore {
  Person find(String name);
}

Yes, that's an interface. No doubt. However, it's not the same interface as in "_program to an interface_"! Sounds strange? Not at all.

Many modern programming languages doesn't have a keyword called "interface". JavaScript has no interface keyword; Ruby has no interface keyword; Scala has not interface keyword; and so on... But it's still possible to _program to an interface_ in all of those languages.

An interface might be a language keyword and an interface might be a design principle. Don't confuse both!

# The language keyword

The language keyword _interface_ can be found in typed languages like Java or C#. It enables the programmer to create an abstract type with multiple concrete implementations.

public interface PersonStore {
  Person find(String name);
}

class DatabasePersonStore implements PersonStore {
  ...
}

class CsvFilePersonStore implements PersonStore {
  ...
}

A consumer can use the interface without even knowing about the concrete implementations.

PersonStore store = PersonStoreFactoy.getPersonStore();
Person person = store.find("Jon Doe");
...

You should use an interface whenever you have multiple implementations. For example if you use a different persistence in production as in development or if you have an algorithm which is interchangeable.

# The design principle

The design principle _interface_ refers to lose coupling between modules or systems.

Whenever you build a larger piece of software, you will have dependencies to other systems. For example to a relational database or to a REST service. All of those dependencies represent tools for your software to fulfil its goals. Such a goal might be to manage contacts in an address bock app or to process orders in an online shop. But whatever the goal of your software is, your goal as a software developer should be to model your problem domain nice and clean.

To achieve such a clean domain layer in your application, you need to create a separation between your domain and external systems. So instead of programming to a relational database, you program to a "data store interface". Instead of programming to a REST service, you program to a "client interface". This will make your application independent of all dependencies and focused on its own domain. If you use MongoDB instead of a relational database tomorrow, it's no problem. All you code is developed to an interface and doesn't even know that there is any database behind it.

[![untitled-drawing](images/Untitled-drawing-1.png)](http://tuhrig.de/wp-content/uploads/2016/11/Untitled-drawing-1.png)

# Pitfalls

So working with interfaces isn't as easy as it sounds like. Here are some typical pitfalls in my opinion.

## #1: Using an interface, but don't program to an interface

If you want to achieve the design principle of programming to an interface, it's very likely that you will use a language construct called interface if your programming language has such (e.g. Java). However, just using an interface keyword, doesn't mean that you are also programming to an interface. Let's make an example:

public interface PersonDatabase {
  PersonEntity find(Long id);
  save(Long id, PersonEntity person);
}

class PersonDatabaseImpl implements PersonDatabase {
  ...
}

In the example above, your dependency (obviously a relational database) defines your interface. Because the database requires a technical ID for each entity, your interface just reflects this. It doesn't hide the fact that you use a database, it even has the word "_Database_" in its name and returns database entities instead of domain objects.

By using such an interface, you don't decouple your software from the other system. Wherever you use the interface, you will know that you work with a database and you will need to deal with the entity classes from the database layer.

## #2: An interface for everything

Of course, we want to do things right. But sometimes, too much is wrong. I have seen some projects where there was an interface for (nearly) everything:

public interface PersonService { ... }
class PersonServiceImpl implements PersonService { ... }

public interface AccountingService { ... }
class AccountingServiceImpl implements AccountingService { ... }

...

Whenever you have an interface which is a one-to-one copy of a class called `...Impl`, something is wrong and you should consider if you really need this interface. There are two rules to think of:

- Use interfaces (the language keyword) if you have multiple concrete implementations.
- Use interfaces (the design principle) to decouple your own system from external system.

So if you have a domain service (e.g. `AccountingService`) there is no need to have an interface (the language keyword) for this service if you don't have multiple types of it (e.g. `CreditCardAccountingService` and `PayPalAccountingService`). Such a service is also part of your own application, so there is no need to decouple yourself from it. There will always be just one single implementation! Our own!

**Best regards,** Thomas
