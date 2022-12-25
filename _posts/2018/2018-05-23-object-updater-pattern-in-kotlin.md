---
layout: post
title: "Object updater pattern in Kotlin"
date: "2018-05-23"
categories: 
  - "coding"
  - "ddd"
  - "design-pattern"
  - "java"
  - "kotlin"
---

Updating data is one of the most frequent use-cases in applications. 
But it might also be a painful task which results in a bunch of ugly code.

I recently stumbled over a typical update scenario in a [Domain-Driven-Design](https://en.wikipedia.org/wiki/Domain-driven_design) (DDD) application. 
In the following, I want to show an elegant solution implemented in [Kotlin](https://kotlinlang.org) (a Java version wouldn't look much different). 
The solution can be described best as a variation of the [builder pattern](https://en.wikipedia.org/wiki/Builder_pattern).

## Scenario

In the scenario I worked on, we had to update a _product_. 
We faced the following requirements:

- A product consists of a bunch of fields such as `name`, `description`, `price` or `category`.
- Fields have to be updated in case the [product information management](https://en.wikipedia.org/wiki/Product_information_management) (PIM) sends a master data update.
- The PIM might send empty fields which we want to ignore.
- The PIM might send duplicate updates which we also want to ignore (because any update involves a subsequent business process).

Based on those requirements our initial code looked like below. 
For every field we needed at least four lines of code looking nearly the same in each case. 
The result was a huge method with `if`s and `if`s and even more `if`s.

```kotlin
class Product {

    private var name: String
    private var description: String

    fun update(command: UpdateCommand) {

        var updateOccurred = false

        if (command.name != null && this.name != command.name) {
            this.name = command.name
            updateOccurred = true;
        }

        if (command.description != null && this.description != command.description) {
            this.description = command.description
            updateOccurred = true;
        }

        if (updateOccurred) {
            // do some business stuff...
        }
    }
}
```

## Object Updater Pattern

To solve the problem we implemented an object called `Updater`. 
It works similar to a builder:

```kotlin
val updateOccurred = Updater()
                        .update(this::name).toValue(command.name)
                        .update(this::description).toValue(command.description)
                        .execute() // returns true or false
```

The implementation looks like below. 
It updates every field with a simple reflection based mechanism. 
By doing this, no public setters are needed. 
All checks (like the not-null-checks) are also performed by the updater object.

```kotlin
class Updater {

    private val updatesToExecute = mutableListOf()

    fun update(field: KMutableProperty0<\*>): Update {
        val updateToExecute = Update(this, field)
        updatesToExecute.add(updateToExecute)
        return updateToExecute
    }

    fun execute(): Boolean {
        return updatesToExecute
            .map(Update::execute)
            .stream()
            .anyMatch { it == true }
    }

    class Update(
        private val updater: Updater,
        private val field: KMutableProperty0<\*>
    ) {

        private var newValue: Any? = null

        fun toValue(newValue: Any?): Updater {
            this.newValue = newValue
            return updater
        }

        fun execute(): Boolean {
            return if (newValue != null && field.get() != newValue) {
                field.isAccessible = true
                field.setter.call(newValue)
                true
            } else false
        }
    }
}
```

## More

- [https://softwareengineering.stackexchange.com/questions/315144/design-pattern-for-object-updates](https://softwareengineering.stackexchange.com/questions/315144/design-pattern-for-object-updates)

**Best regards,** Thomas.
