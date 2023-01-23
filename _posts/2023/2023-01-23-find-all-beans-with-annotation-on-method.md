---
layout: post
title: "Find all beans with annotation on method"
date: "2023-01-23"
categories: 
  - "coding"
  - "java"
  - "spring"
---

If you have ever worked with an event bus like [Kafka](https://kafka.apache.org/), [Kinesis](https://aws.amazon.com/kinesis/) or [ActiveMQ](https://activemq.apache.org/), 
I'm sure you saw some code like below. 
A method annotated as some kind of event-listener.
Although every annotation is slightly different, the pattern is all the same.
But how are those methods picked-up by [Spring](https://spring.io/)?

```java
@KafkaListener(topics = "orderSubmitted")
public void handle(String event) {
    // ...
}

@KinesisListener(stream = "order-submitted-event")
public void handle(OrderSubmittedEvent event) {
    // ...
}

@JmsListener(destination = "orderSubmitted")
public void handle(OrderSubmitted event) {
    // ...
}
```

## Defining a custom annotation

For this example, we introduce a custom annotation. 
In [Kotlin](https://kotlinlang.org/) this would look like this.
The annotation is called `@MyEventListener` and takes a single argument - the name of the event to listen for.
   
```kotlin
@Target(AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME)
annotation class MyEventListener(val event: String)
```

A complete event listener would look like this:

```kotlin
@Component
class EventListeners {
    
    @MyEventListener(event = "order-submitted-event")
    fun handle(event: OrderSubmittedEvent) {
        // ...
    }

    @MyEventListener(event = "order-cancelled-event")
    fun handle(event: OrderCancelledEvent) {
        // ...
    }
}
```

So how can we pick up those methods?

## Finding all beans

The first step is to get a list of all beans managed by Spring.
We can do this by using the `ApplicationContext` which gives us access to all available beans.
The only thing we must be careful with, is to wait until the `ApplicationContext` is ready to use.
In Kotlin, this would look like this:

```kotlin
@EventListener
fun applicationReady(event: ApplicationReadyEvent) {
    val allAvailableBeans = getAllBeans()
    println(allAvailableBeans)
}

private fun getAllBeans(): List<Any> {
    return applicationContext
        .beanDefinitionNames
        .map { applicationContext.getBean(it) }
}
```

## Finding beans with annotated methods

After we have the list of all beans, we can search for methods with our annotation.

```kotlin
val beansWithOurMethodAnnotation = allAvailableBeans.filter {
    AopUtils
        .getTargetClass(it)
        .methods
        .any { it.isAnnotationPresent(MyEventListener::class.java) }
}
```

This looks obvious except of one step: what is `AopUtils.getTargetClass(it)`?
To answer this question, we must take a closer look at how the Spring framework works.

Spring uses [proxies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-introduction-proxies) to handle cross-cutting concerns such as transactions.
This means that a bean is not called directly, but by using a proxy.
The proxy wraps the actual bean and takes care of things such as transactions (before and after calling the actual class).

![](/images/2023/01/aop-proxy-call.png)

(Picture taken from [Spring Docs 3.0.0.M3](https://docs.spring.io/spring-framework/docs/3.0.0.M3/reference/html/ch08s06.html))

In your debugger this looks something like this:

![](/images/2023/01/aop-debugger.png)

The tricky thing is, that the proxy does not have a method annotated with our annotation.
Only the target class wrapped by the proxy has this annotation.
So we need to unwrap the class inside the proxy before looking for methods with our annotation.

## Invoking our annotated methods

Great, we found all beans which have a method annotated with our customer annotation!
But what can we do with them now? 
Of course, we can call them!

```kotlin
val eventName = "order-submitted-event"
val eventJson = """{ "id":"o-2023-01", "customer":"c-21331", "items":[ ... ] }"""

allAvailableBeans.forEach { bean ->
    AopUtils
        .getTargetClass(bean)
        .methods
        .filter { it.isAnnotationPresent(MyEventListener::class.java) }
        .filter { it.getAnnotation(MyEventListener::class.java).event == eventName }
        .forEach { method ->
            val eventClass = method.parameterTypes[0]
            val event = objectMapper.readValue(eventJson, eventClass)
            method.invoke(bean, event)
        }
}
```

## An example use-case

At [Bringmeister](https://www.bringmeister.de/), we used this technique to provide REST-controllers for our Kinesis event listeners.
The event listeners are called by any incoming record from Kinesis. 
But sometimes it's good to have a simple way for providing test data and debugging.
So we implemented a generic REST-controller to invoke any Kinesis event listeners.
It looks like this.
The REST-controller makes it super easy to send some JSON to a Kinesis event listener.
It's very convenient.

```kotlin
@RestController
class KinesisListenerController(private val objectMapper: ObjectMapper) {

    private var listeners: List<Any> = emptyList()
    
    @PutMapping("/kinesis/streams/{stream}")
    fun event(@PathVariable stream: String, @RequestBody json: String) {
        listeners
            .forEach { bean ->
                AopUtils
                    .getTargetClass(bean)
                    .methods
                    .filter { it.isAnnotationPresent(KinesisListener::class.java) }
                    .filter { it.getAnnotation(KinesisListener::class.java).stream == stream }
                    .forEach { method ->
                        val eventClass = method.parameterTypes[0]
                        val event = objectMapper.readValue(json, eventClass)
                        method.invoke(bean, event)
                    }
            }
    }

    @EventListener
    fun applicationReady(event: ApplicationReadyEvent) {
        listeners = getAllBeans()
            .filter {
                AopUtils
                    .getTargetClass(it)
                    .methods
                    .any { it.isAnnotationPresent(KinesisListener::class.java) }
            }
    }

    private fun getAllBeans(): List<Any> {
        return applicationContext
            .beanDefinitionNames
            .map { applicationContext.getBean(it) }
    }
}

```

## More

- [Spring Application Context Events](https://www.baeldung.com/spring-context-events)
- [Spring AOP Proxies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-introduction-proxies)

**Best regards,** Thomas.
