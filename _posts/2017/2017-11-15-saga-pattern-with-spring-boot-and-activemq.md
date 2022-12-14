---
layout: post
title: "Saga pattern with Spring Boot and ActiveMQ"
date: "2017-11-15"
categories: 
  - "coding"
tags: 
  - "activemq"
  - "data-mining"
  - "java"
  - "saga"
  - "spring"
coverImage: "road-2898258_1280.jpg"
---

During the last days, I dug a little bit into the [Saga pattern](http://microservices.io/patterns/data/saga.html). The Saga pattern is an architectural pattern which provides an alternative approach to big and long running ACID transactions. It takes a business process and breaks it up into small isolated steps - each of them with its own transaction. The overall consistency is provided by manually reverting past actions.

# Example

Imagine a system to submit reviews (very much like I described in my [boilerplate project](http://tuhrig.de/spring-boot-boilerplate-project-with-activemq-and-angularjs)). An user can submit a review which is checked (by a person or an algorithm) and either approved or rejected. We can implement this behaviour with a couple of events:

- `REVIEW_SUBMITTED_EVENT` - The first event. It starts our business process as well as the saga.
- `REVIEW_IN_EXAMINATION_EVENT` - A second arbitrary event. It represents an action such as informing the customer via e-mail. In this example, it's just a placeholder to make everything a little bit more complicated.
- `REVIEW_APPROVED_EVENT` - The last event. In this case, the checking was successful and the review is accepted. There's nothing more to do. This event ends our saga.
- `REVIEW_REJECTED_EVENT` - A compensating event. It also ends our business process, but in the way of a rollback. For example, it deletes the rejected review.

# The saga

How could a saga look like? A saga basically encapsulates the individual steps of a business process. It keeps track of what has happened and what is still missing.

Note that there are many ways a saga could look like. None of them is right or wrong. The following just show a simple approach to solve the example above.

public class Saga {

    // The name/type of the saga, e.g.: "ReviewCheckingSaga". There will exist many 
    // "instances" of a saga. For example, every review will start it's own new saga.
    private String name;

    // Unique key of the saga, e.g. the ID of the review which is checked.
    private String key;

    // A reference to a service which is called by the saga in case an event occurs.
    // In my example, it's the name of a Spring Bean ("reviewCheckingService").
    private String handler;

    // The names of all events which make up the saga, e.g.: 
    // { "REVIEW\_SUBMITTED\_EVENT", "REVIEW\_IN\_EXAMINATION\_EVENT", "REVIEW\_APPROVED\_EVENT }
    private List requiredEvents = new ArrayList<>();

    // The name of the rollback event which cancels the saga. Note that I've just used a 
    // single event, but it would be perfectly fine to use a list of events here. Just as
    // you need.
    private String rejectionEvent;

    // All events which already have occurred.
    private final List events = new ArrayList<>();

    // getter, setter and more stuff we see later on
}

As soon as we get a `REVIEW_SUBMITTED_EVENT` we create a new saga to represents a new checking-process:

@JmsListener(destination = "MY\_EVENT\_TOPIC", selector = "\_type = 'REVIEW\_SUBMITTED\_EVENT'")
public void onEvent(ReviewSubmittedEvent event) {
    Saga saga = Saga.builder()
                    .name("ReviewCheckingSaga")
                    .key(event.getReviewId())
                    .handler("reviewCheckingService")
                    .requiredEvent(ReviewSubmittedEvent.class.getName())
                    .requiredEvent(ReviewInExaminationEvent.class.getName())
                    .requiredEvent(ReviewApprovedEvent.class.getName())
                    .rejectionEvent(ReviewRejectedEvent.class.getName())
                    .build();
    sagaService.save(saga);
    sagaService.apply("ReviewCheckingSaga", event.getReviewId(), event);
}

After saving the saga, we "apply" the first event to it.

# Event handling

For handling events, ActiveMQ is used. There's a JMS listener for each event and - _except of the initial setup of the saga_ - every event listener looks the same.

@JmsListener(destination = "MY\_EVENT\_TOPIC", selector = "\_type = 'REVIEW\_SUBMITTED\_EVENT'")
public void onEvent(ReviewSubmittedEvent event) {
    Saga saga = Saga.builder()
                    // ...setting up the saga as shown above...!
                    .build();
    sagaService.apply("ReviewCheckingSaga", event.getReviewId(),event);
}

@JmsListener(destination = "MY\_EVENT\_TOPIC", selector = "\_type = 'REVIEW\_IN\_EXAMINATION\_EVENT'")
public void onEvent(ReviewInExaminationEvent event) {
    sagaService.apply("ReviewCheckingSaga", event.getReviewId(), event);
}

@JmsListener(destination = "MY\_EVENT\_TOPIC", selector = "\_type = 'REVIEW\_APPROVED\_EVENT'")
public void onEvent(ReviewApprovedEvent event) {
    sagaService.apply("ReviewCheckingSaga", event.getReviewId(), event);
}

@JmsListener(destination = "MY\_EVENT\_TOPIC", selector = "\_type = 'REVIEW\_REJECTED\_EVENT'")
public void onEvent(ReviewRejectedEvent event) {
    sagaService.apply("ReviewCheckingSaga", event.getReviewId(), event);
}

By calling `apply(...)` we give the control flow to the saga. Instead of the listener itself or a service, the saga will decide what to do. It knows the current status of the overall process as it stores all previous events. 

A lot of things can happen now:

  - Check if a saga exists
  - Check if the event is required and belongs to the saga
  - Check if the event has already occurred (_duplicate_)
  - Check if the event came in the right order
  - Invoke a handler method for the event on the handler associated with the sage (_reviewCheckingService_)
  - Check if the sage is completed by the event
  - Check if the sage is cancelled by the event

Note that those steps are only suggestions. You can do one of them or all of them. It totally belongs to your use-case. Maybe you process doesn't require a certain order of the events or don't mind about duplicate events.

# Doing things

We already defined how a saga could look like and how we listen for events to apply them to the saga. The saga decides what to do with the event and might call a handler method on the service.

public void apply(String sagaName, String key, Object event) {
    Saga saga = sagaRepository.get(sagaName, key);
    Object handler = applicationContext.getBean(saga.getHandler());
    try {
        Method on = handler.getClass().getMethod("on", event.getClass());
        ReflectionUtils.invokeMethod(on, handler, event);
    } catch (NoSuchMethodException e) {
        log.debug("No event handler found");
    }
}

Note: The code snippet above is just an example to illustrate the invocation of the handler. It leaves out a lot of things: check if the event is required, check for duplicate events, etc. It also ignores if no handler method exists and relies on a naming convention for the handler (the handler method must be called `on(...)`).

When applying an event, we can also check if it completes the saga. If so, we can execute some special handler method:

public void apply(String sagaName, String key, Object event) {
    Saga saga = sagaRepository.get(sagaName, key);
    Object handler = applicationContext.getBean(saga.getHandler());
    try {
        // ...
    }
    if(allEventsOccurred(saga)) {
        Method resolve = handler.getClass().getMethod("resolve", Saga.class);
        ReflectionUtils.invokeMethod(resolve, handler, saga);
    }
}

# Rollback

So far, we only considered the happy path. Every event comes in the right order, no duplicates and no exceptions. But what happens in case of an error?

In a traditional ACID transaction, we would do a database rollback (instead of a commit). However, this breaks as soon as we do things outside of the database like REST calls, sending an e-mail or writing to a file. We cannot rollback those things. Even worse: If we rollback the database, we might have inconsistencies as the other tasks are not rolled back. The REST is made, the e-mail is sent and the file is written.

Instead of a technical rollback (which is very limited) the Saga pattern uses _compensating events_ to react to exceptions. If something goes wrong, a compensating event is thrown which "cancels" the Saga and reverts all things done.

In the example above, the compensating event is the `REVIEW_REJECTED_EVENT`. It tells use that we must revert things. Note that we cannot relay on a technical solution. Instead we must write business code to explicitly handle the error situation. E.g. we must delete the review from the database, remove a line from a CSV file or we must send an e-mail with an apology.

public void apply(String sagaName, String key, Object event) {
    Saga saga = sagaRepository.get(sagaName, key);
    Object handler = applicationContext.getBean(saga.getHandler());
    try {
        // ...
    }
    if(allEventsOccurred(saga)) {
        // ...
    }
    if(isErrorEvent(saga, event)) {
        Method reject = handler.getClass().getMethod("reject", Saga.class);
        ReflectionUtils.invokeMethod(reject, handler, saga);
    }
}

**Best regards,**
Thomas

# More

  - [http://microservices.io/patterns/data/saga.html](http://microservices.io/patterns/data/saga.html)
  - [https://medium.com/@roman01la/confusion-about-saga-pattern-bbaac56e622](https://medium.com/@roman01la/confusion-about-saga-pattern-bbaac56e622)
  - [https://www.youtube.com/watch?v=xDuwrtwYHu8](https://www.youtube.com/watch?v=xDuwrtwYHu8)
