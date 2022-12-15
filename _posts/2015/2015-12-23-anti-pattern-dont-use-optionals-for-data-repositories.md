---
layout: post
title: "Anti Pattern: Don't use Optionals for data repositories"
date: "2015-12-23"
categories: 
  - "coding"
  - "design-pattern"
  - "java"
---

## Java 8 Optionals

Java 8 introduced a new feature call _[Optionals](http://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html)_. Optionals are an easy way to force the caller of a method to deal with a possible `null` return value:

    public Optional getName() {
        ...
    }
    
    Optional name = getName();
    System.out.println(name.orElse("I forgot my name"); 

If you use an Optional as the return type of your method, whoever calls the method will be reminded that it might return null. If you use Optionals, you don't need to think about `NotFoundException`s, special return types or JavaDoc comments which state that the method might return null. **An Optional says more than a thousand comments!**

    /\*\*
     \* @return The name or null if the name has been forgotten
     \*\*/
    public String getName() {
        ...
    }

## Optionals might just postpone the problem

But an Optional doesn't do one thing: it doesn't answer the question what to do if there is no method result. **It just postpones the problem to the caller.** This might be OK if `null` is a quite expected result of the method, but in most cases `null` means something went wrong. Don't postpone this!

## An example

Nearly every application stores data. Whenever data is stored, it will be loaded again which might go wrong as the data simply couldn't be found. That sounds like a good use case for Optionals! Let's see how it could look like:
    
    public interface PersonRepository {
        Optional findOneById(Long id);
    } 

Nice! If there is no Person with the given ID the repository will return an empty Optional and the caller has to deal with it. Technically, this is a nice and clean solution. But from the business perspective it isn't. The real question is: **Can it happen that we look for an ID which is not present?** If yes, than an Optional is a good solution, but I would doubt that in many case.

Whenever you attempt to load data by its ID, it must be present! There are hardly any cases where you guess or search for an ID. Such IDs are like references or pointers in a programming language: they must point to something and returning an ID which points to nothing is a very bad idea. A typical situation where you load data by its ID goes like this: You provide a list of data (maybe a result of a search) and you provide a detail view on a single entry. So, you show the list in your UI and on a click on of the elements, the details of ID _XYZ-123_ are shown. This set of data must be there! **Don't use an optional here!**

## Never return _null_

Back in the days (which means before Java 8), there was a rule which said that you shall never return null. This rule was always a bit tricky to follow as it might lead to a lot of hidden traps. People started to return empty objects, special objects, return codes and other crazy stuff. If you did this in the past and you are using Optionals now, you definitely improved! But you should still ask you, why there might be no result and if it is OK to have no result. Not only from a technical perspective but also from the business perspective.

**Optional is just a new name for _null_.**

## When to use Optionals

The actual use case for Optionals is functional programming in Java 8. If you use this, you will filter, map or reduce data and you will often come to the point where there might be the possibility that no data exists. It's a good idea to use Optionals here, as you often write this code in a fluent style. A null pointer is really ugly to handle here. But the main difference is another: You do a search or filtering which perfectly could have no result. This is the main point for using an Optional. Don't use an Optional, because you don't want to deal with _null_ (you need to do it anyway later!), use it because you have a really good reason to return null.

**Best regards,** Thomas.
