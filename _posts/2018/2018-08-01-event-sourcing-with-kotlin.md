---
layout: post
title: "Event Sourcing with Kotlin"
date: "2018-08-01"
categories: 
  - "coding"
  - "ddd"
  - "design-pattern"
  - "kotlin"
---

Some weeks ago I published a [demo project on GitHub](https://github.com/bringmeister/ddd-with-kotlin) showing a [Domain Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design) with [Kotlin](https://kotlinlang.org). You can find the original blog post right [here](https://tuhrig.de/ddd-with-kotlin). Now I took the approach one step further and added [Event Sourcing](http://microservices.io/patterns/data/event-sourcing.html) to the demo. Here's what I did.

## Event Sourcing

The basic idea of Event Sourcing is to store a stream of events instead the current state of an object. So Event Sourcing is a different persistence approach.

Traditionally, we would store the current state of an object in a relational database. We would map the object to tables and columns and update them every time the object changes.

    class Product {
        val id = 42
        fun update(...) {
            this.name = “Coca-Cola”
            this.price = 1.99
        }
    }

    repository.save(product)

    ID | Name       | Price
    ---|------------|----------
    42 | Coca-Cola  | 1.99
    43 | Water      | 0.99

Event Sourcing takes a different approach. Instead of saving the object, all changes to the object are saved. In Domain Driven Design and Event Sourcing those changes are called _events_. The sum of all events make up the current state of the object.

    class Product {
        val id = 42
        fun update(...) {
            ...
            return ProductUpdatedEvent(42, “Coca-Cola”, 1.99)
        }
    }
    
    eventStore.save(event)
    
    ID | Type                | Data
    ---|---------------------|-----------------------------
    42 | ProductCreatedEvent | { 42, Cola, null }
    42 | PriceUpdatedEvent   | { 42, 0.00 }
    42 | ProductUpdatedEvent | { 42, Coca-Cola, 1.99 }
    43 | ProductUpdatedEvent | { 43, Water, 0.99 }

The benefit of this type of persistence is that we always keep the whole history of an object. We can see each and every change which occurred over time.

The downside is a more complicated and unfamiliar programming concept:

To implement Event Sourcing all changes must be published via events. Even the smallest change to our domain model (setting some flag from `true` to `false`) must result in an event. Any data which is not part of an event will be lost.

Having those events, we must implement two functionalities: **(1)** First we need a business method which takes an input, does things (_calculations_, _calls_, _conversions_, etc.) and finally throws an event with all data involved. **(2)** Second we need another method which takes the event and simply applies it to the model.

    class Product {
    
        fun update(...) {
            // Business logic with calculations, calls, etc...
            return ProductUpdatedEvent(...)
        }
    
        fun apply(ProductUpdatedEvent) {
            // Apply data! Don’t do anything else!
            this.name = event.name
            this price = event.price
        }
    }

The first method performs business logic and actually does something whereas the second function is just "a setter for data". We need this differentiation, because whenever we load an object all events from the database must be applied to it. This must be done without triggering business logic and side effects.

    val events = eventStore.findAllById(42)
    val product = Product().applyAll(events) 

## Demo on GitHub

As this description might sound a little bit weird, I've prepared a small demo project on [GitHub](https://github.com/bringmeister/event-sourcing-with-kotlin):

> [https://github.com/bringmeister/event-sourcing-with-kotlin](https://github.com/bringmeister/event-sourcing-with-kotlin)

The demo shows a small use-case and provides a web UI which helps to see what is happening behind the scenes:

[![](images/Screen-Shot-2018-08-01-at-11.15.57-1024x713.png)](http://tuhrig.de/wp-content/uploads/2018/08/Screen-Shot-2018-08-01-at-11.15.57.png)

## Presentation

https://speakerdeck.com/tuhrig/event-sourcing-1

## More

- [https://github.com/bringmeister/event-sourcing-with-kotlin](https://github.com/bringmeister/event-sourcing-with-kotlin)
- [http://microservices.io/patterns/data/event-sourcing.html](http://microservices.io/patterns/data/event-sourcing.html)

**Best regards,** Thomas.
