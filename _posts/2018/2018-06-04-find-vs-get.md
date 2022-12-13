---
layout: post
title: "find vs. get"
date: "2018-06-04"
categories: 
  - "coding"
tags: 
  - "architecture"
  - "database"
  - "design-pattern"
coverImage: "doors-1767562_1280.jpg"
---

Three years ago I wrote a [blog post](https://tuhrig.de/anti-pattern-dont-use-optionals-for-data-repositories) with the provoking title "**Don’t use Optionals for data repositories**". The post received a couple of critical comments and I had the feeling that I didn't made my point clear.

This week I stumbled over the same topic again, but from a slightly different point of view. I tried to design some repository interfaces and got stuck on the question about how to name my methods. Here's what I thought about.

# A repository interface

Repository interfaces typically implement the same set of methods. For example the [CRUD repository of Spring Data](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html) has amongst others the following methods:

public interface CrudRepository {
    ...
    Optional findById(ID id);
    Iterable findAll();
    ...
} 

So is there anything wrong with these methods? - No, not at all. But let's compare it to another repository interface from Spring Data - [the JPA repository](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html):

public interface JpaRepository {
    ...
    T getOne(ID id);
    List findAll();
    ...
} 

Also, this interface is perfectly fine. However, there's a small but important difference between the two repositories: while the CRUD interface expects to find nothing, the JPA interface expects to always find something.

# find vs. get

The difference between the two interfaces lies in the semantic of their methods. The CRUD repository "finds" something whereas the JPA repository "gets" something. While "find" might lead to no result at all, "get" will always return something - otherwise the JPA repository [throws an exception](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html#getOne-ID-).

# The rules

This semantic leads to the following rules:

- Return an `Optional` if you try to _find_, _search_ or _look-up_ something. Finding nothing is an expected outcome in this case. The caller of the method must deal with it.
- Return the entity or throw an exception if it doesn't exist in case you reference it directly (_get_ or _load_). In this case, it's not a valid state if the entity doesn't exist - it's an exception. However, by throwing an exception the caller doesn't need to handle the inconsistency of the persistence layer.
- Return an (possibly empty) list in case of multiple elements in a result. In this case, it doesn't matter if you have something or not (== the list is empty). It's transparent for the caller.
- Never return null. It's the at least meaningful return value you can choose.

# Use cases

Having said that, what are suitable use cases for _find_ and _get_?

Let's make an example and assume that we've implemented a book shop. In this book shop an user can search for a book by its title. In this case, it's perfectly fine that we don't find anything. The user might have made a typo or the book just doesn't exist. So we would use something similar to `Optional findById(ID id);`.

However, if we go one step further we run into another use case. Let's assume the user has found a book and made an order. Eventually, we want to send an invoice to the user to get our money. To do so, we load the order and look-up the book the user bought to get its price. In this case, the book must exist! Otherwise something is really broken. So we would use something like `T getOne(ID id);`.

Of course, a real world scenario would be much more complicated. The search would probably use a search index like Solar and the order would possible have a foreign key on the book which ensures the integrity at the database level.

# More

- [https://softwareengineering.stackexchange.com/questions/182113/how-and-why-to-decide-between-naming-methods-with-get-and-find-prefixes](https://softwareengineering.stackexchange.com/questions/182113/how-and-why-to-decide-between-naming-methods-with-get-and-find-prefixes)

**Best regards,** Thomas
