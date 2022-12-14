---
layout: post
title: "Messages vs. Events vs. Commands"
date: "2017-12-10"
categories: 
  - "coding"
tags: 
  - "activemq"
  - "architecture"
  - "design-pattern"
  - "jms"
coverImage: "scenery-2981200_1280.jpg"
---

In a distributed environment, services must communicate with each other. One of the most popular ways of communication is messaging. Tools like [ActiveMQ](http://activemq.apache.org), [RabbitMQ](https://www.rabbitmq.com) or [Kafka](http://kafka.apache.org) are making it easy to exchange messages between systems. But no matter which broker you are using, you must decide which kind of message you want to send.

# Messages

Message brokers deal with messages. They are the basic unit of communication and can literally be anything. An ID, a string, an object, a command, an event or whatever. Messages have no special intent.

[![](images/message-event-command.png)](http://tuhrig.de/wp-content/uploads/2017/12/message-event-command.png)

Having no special intent makes messages generic but also less meaningful. That's why we can use two more concepts on top of messages.

# Events

An [event](http://www.enterpriseintegrationpatterns.com/patterns/messaging/EventMessage.html) is a message which informs various listeners about something which has happened. It's send by a producer which doesn't know and doesn't care about the consumers of the event.

[![](images/event.png)](http://tuhrig.de/wp-content/uploads/2017/12/event.png)

**Example:** A typical example would be an online shop. Whenever an order is placed, the shop would publish an `OrderSubmittedEvent` to inform other systems (e.g. the logistics system) about the new order. However, the shop doesn't care - or even know - about the consumers. If the logistics system isn't interested in the events anymore, it just unsubscribes from the list of consumers.

⇒ In the world of JMS, events would be messages send to a topic. ⇒ The dependency goes from the consumer to the producer.

# Commands

A [command](http://www.enterpriseintegrationpatterns.com/patterns/messaging/CommandMessage.html) on the order side is much more specific. It's a one-to-one connection between a producer (who sends the command) and a consumer (who takes and executes the command).

[![](images/command.png)](http://tuhrig.de/wp-content/uploads/2017/12/command.png)

**Example:** In case of an online shop, one such example could be the `BillCustomerCommand`. After an order is placed, the online shop sends this command to the billing system to trigger the invoice.

⇒ In the world of JMS, commands would be messages send to a queue. ⇒ The dependency goes from the producer to the consumer.

# The intent

The difference between _messages_, _events_ and _commands_ lies in their intent. While messages have no special intent at all, events inform about something which has happened and is already completed (in the past). Commands trigger something which should happen (in the future).

# More

- [https://www.techwalla.com/articles/jms-queue-vs-jms-topic#ixzz1I1rcTSdS](https://www.techwalla.com/articles/jms-queue-vs-jms-topic#ixzz1I1rcTSdS)
- [https://stackoverflow.com/questions/4962755/why-are-commands-and-events-separately-represented](https://stackoverflow.com/questions/4962755/why-are-commands-and-events-separately-represented)
- [http://www.enterpriseintegrationpatterns.com/patterns/messaging/EventMessage.html](http://www.enterpriseintegrationpatterns.com/patterns/messaging/EventMessage.html)
- [http://www.enterpriseintegrationpatterns.com/patterns/messaging/CommandMessage.html](http://www.enterpriseintegrationpatterns.com/patterns/messaging/CommandMessage.html)

**Best regards,** Thomas
