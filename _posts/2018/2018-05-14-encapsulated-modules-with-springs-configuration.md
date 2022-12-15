---
layout: post
title: "Encapsulated modules and clean tests with Spring's @Configuration"
date: "2018-05-14"
categories: 
  - "coding"
  - "architecture"
  - "design-pattern"
  - "spring"
---

Usually we use Spring's `@Configuration` to define some special beans. A classic example is the definition of a data source:

    @Bean
    public DataSource dataSource() {
        DataSource dataSource = new DataSource();
        dataSource.setUsername(username);
        // ... and so on ...
        return dataSource;
    }

However, we can also use Spring's `@Configuration` to build better modules which helps us to writer cleaner tests. Let's go through an example.

## An example

Let's assume we have an application which uses domain events. After something has happened (e.g. _an order has been submitted_) an event is thrown (`OrderSubmittedEvent`). To send those events, we have defined an interface:

    public interface EventPublisher {
        void publish(DomainEvent domainEvent);
    }

Further assume, that we have decided to use [Guava's EventBus](https://github.com/google/guava/wiki/EventBusExplained) for the implementation of our `EventPublisher` interface. The project might look like this now:

    com.my.company
    +-------------/domain
                  +------/EventPublisher.java
    +-------------/infrastructure
                  +--------------/guava
                                 +-----/GuavaConfiguration.java
                                 +-----/GuavaEventPublisher.java
                                 +-----/GuavaSubscriberPostProcessor.java

What we can see:

- A configuration called `GuavaConfiguration.java`. This configuration contains a single `@Bean` definition for an instance of Guava's `EventBus`.
- An implementation of our `EventPublisher` interface which is using the `EventBus` bean defined in the configuration.
- A class which will look-up all event listeners on start-up and register them at the `EventBus` bean.

## The module

What we can see in the example above is a module with **high cohesion** and **low coupling** to the outside world. The cohesion is high, because all classes depend on each other (either by source code dependencies or logical dependencies) and implement a single feature (sending events with Guava). The coupling is low, because we don't use any of these classes directly - we always use the `EventPublisher` interface.

However, here comes the critical point: We have exposed much more internal information from our module than needed.

    @Configuration
    public class GuavaConfiguration {
    
        public EventBus eventBus() {
            return new AsyncEventBus(Executors.newFixedThreadPool(10))
        }
    }

Some "lazy developer" could just inject the `EventBus` and send events without using our `EventPublisher` interface. Layers and encapsulation would be broken!

## A better configuration

We can solve this problem with a better configuration and by removing all `@Component` / `@Service` annotations from the classes of our module. Such a configuration comes with more boilerplate code, but exposes less to the outside world. The `EventBus` remains an internal detail of the module.

    @Configuration
    public class GuavaConfiguration {
    
        private final EventBus eventBus = new AsyncEventBus(Executors.newFixedThreadPool(10))
    
        @Bean
        public EventPublisher guavaCommandBus() {
            return new GuavaEventPublisher(eventBus)
        }
    
        @Bean
        public GuavaSubscriberPostProcessor guavaSubscriberPostProcessor() {
            return new GuavaSubscriberPostProcessor(eventBus)
        }
    }

## Cleaner tests

Another benefit of such a "complete module configuration" are cleaner tests. Let's assume you have test case which either tests the module itself or needs the module for some integration testing. The declaration of the test would look like this _without_ the module configuration:

    @RunWith(SpringRunner.class)
    @SpringBootTest(classes = {
        GuavaConfiguration.class,
        GuavaCommandBus.class,
        GuavaSubscriberPostProcessor.class
    })
    public class MyTestWhichNeedsTheModule { ... }

One would need to manually import each class of the module. This is not only **error-prone**, but also requires **knowledge of the internal dependencies** of the module - which are likely to change.

On the other hand, the test becomes quite simple a self-containing configuration. One would just need to import the configuration of the whole module - done. This makes integration tests much easier.

    @RunWith(SpringRunner.class)
    @SpringBootTest(classes = {
        GuavaConfiguration.class
    })
    public class MyTestWhichNeedsTheModule { ... }

## Dependencies to other modules

Not every module is independent. In reality, a lot of modules would depend on other modules. For example, let's assume that we have a module which generates unique IDs for all kinds of requests (HTTP requests, events...). The ID will be part of the meta data / header of those requests and can be used for logging and tracing. Such a module could look like this:

    com.my.company
    +-------------/infrastructure
                  +--------------/tracing
                                 +-----/TracingConfiguration.java
                                 +-----/TraceGenerator.java

Now assume that the Guava module depends on this module, because every event should get an unique tracing ID before it is send. Introducing such a dependency can be very painful as it breaks a lot of tests. Actually, it breaks all tests which are using the Guava module, but are missing the new tracing module. We would need to touch each and every test case and add the missing tracing dependency:

    @RunWith(SpringRunner.class)
    @SpringBootTest(classes = {
        GuavaConfiguration.class,
        TracingConfiguration.class // add a new dependency!
    })
    public class MyTestWhichNeedsTheModule { ... }

Again, this breaks encapsulation as we need to know that the Guava module depends on another module. To solve this problem, we can explicitly import the tracing module in the Guava configuration:

    @Configuration
    @Import(TracingConfiguration.class) // Declare the dependency!
    public class GuavaConfiguration {
        // ...
    }

By doing this, we clearly state that a dependency exists and we get a self-contained module configuration again. **No test needs to be changed.**

## There's even more

Well designed modules have even more benefits. Tom Hombergs describes [in his blog](https://reflectoring.io/spring-boot-modules) how modules can be used to enable (or disable) certain features and how to make a difference between technical modules and business modules. It's an interesting read I can recommend.

## More

- [https://reflectoring.io/spring-boot-modules](https://reflectoring.io/spring-boot-modules)

**Best regards,** Thomas.
