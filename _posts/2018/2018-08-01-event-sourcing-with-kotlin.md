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

Some weeks ago I published a [demo project on GitHub](https://github.com/tuhrig/ddd-with-kotlin) showing a [Domain Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design) with [Kotlin](https://kotlinlang.org). 
You can find the original blog post right [here](/ddd-with-kotlin). 
Now I took the approach one step further and added [Event Sourcing](http://microservices.io/patterns/data/event-sourcing.html) to the demo. 
Here's what I did.

## Event Sourcing

The basic idea of Event Sourcing is to store a stream of events instead the current state of an object. 
So Event Sourcing is a different persistence approach.

Traditionally, we would store the current state of an object in a relational database. 
We would map the object to tables and columns and update them every time the object changes.

```kotlin
class Product {
    val id = 42
    fun update(...) {
        this.name = “Coca-Cola”
        this.price = 1.99
    }
}

repository.save(product)
```

Saves data like this:

```
ID | Name       | Price
---|------------|----------
42 | Coca-Cola  | 1.99
43 | Water      | 0.99
```

Event Sourcing takes a different approach. 
Instead of saving the object, all changes to the object are saved. 
In Domain Driven Design and Event Sourcing those changes are called _events_. 
The sum of all events make up the current state of the object.

```kotlin
class Product {
    val id = 42
    fun update(...) {
        ...
        return ProductUpdatedEvent(42, “Coca-Cola”, 1.99)
    }
}

eventStore.save(event)
```

Saves data like this:

```
ID | Type                | Data
---|---------------------|-----------------------------
42 | ProductCreatedEvent | { 42, Cola, null }
42 | PriceUpdatedEvent   | { 42, 0.00 }
42 | ProductUpdatedEvent | { 42, Coca-Cola, 1.99 }
43 | ProductUpdatedEvent | { 43, Water, 0.99 }
```

The benefit of this type of persistence is that we always keep the whole history of an object. 
We can see each and every change which occurred over time.

The downside is a more complicated and unfamiliar programming concept:

To implement Event Sourcing all changes must be published as events. 
Even the smallest change to our domain model (setting some flag from `true` to `false`) must result in an event. 
Any data which is not part of an event will be lost.

Having those events, we must implement two functionalities: 

1. First we need a business method which takes an input, does things (_calculations_, _calls_, _conversions_, etc.) and finally throws an event with all data involved. 
2. Second we need another method which takes the event and applies it to the model.

```kotlin
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
```

The first method performs business logic and actually does something whereas the second function is just "a setter for data". 
We need this differentiation, because whenever we load an object all events from the database must be applied to it. 
This must be done without triggering business logic and side effects.

```kotlin
val events = eventStore.findAllById(42)
val product = Product().applyAll(events) 
```

## Demo on GitHub

As this description might sound a little bit weird, I've prepared a small demo project on [GitHub](https://github.com/tuhrig/event-sourcing-with-kotlin):

> [https://github.com/tuhrig/event-sourcing-with-kotlin](https://github.com/tuhrig/event-sourcing-with-kotlin)

The demo shows a small use-case and provides a web UI which helps to see what is happening behind the scenes:

![](/images/2018/08/event-sourcing.png)

## Presentation

<iframe
name="thirdPartyContent"
class="speakerdeck-iframe"
frameborder="0"
allowfullscreen="true"
mozallowfullscreen="true"
webkitallowfullscreen="true"
data-ratio="1.3333333333333333">
</iframe>

<script>
if(consentGiven()) {
    const site = "https://speakerdeck.com/player/81ce3989e09d4582b7a59ed6c05e9af3";
    document.getElementsByName('thirdPartyContent')[0].src = site;
}
</script>

- See: [https://speakerdeck.com/tuhrig/event-sourcing-1](https://speakerdeck.com/tuhrig/event-sourcing-1)
- PDF: [📄 Event_Sourcing.pdf](/assets/pdf/Event_Sourcing.pdf)

## More

- [https://github.com/bringmeister/event-sourcing-with-kotlin](https://github.com/bringmeister/event-sourcing-with-kotlin)
- [http://microservices.io/patterns/data/event-sourcing.html](http://microservices.io/patterns/data/event-sourcing.html)

**Best regards,** Thomas.
