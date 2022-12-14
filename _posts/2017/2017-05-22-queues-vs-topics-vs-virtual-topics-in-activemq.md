---
layout: post
title: "Queues vs. Topics vs. Virtual Topics (in ActiveMQ)"
date: "2017-05-22"
categories: 
  - "coding"
  - "architecture"
  - "design-pattern"
  - "software-design"
---

[ActiveMQ](http://activemq.apache.org) provides a variety of different messaging patterns. 
While queues and topics are the most known ones, virtual topics can combine the best of both worlds: 
multiple consumers with their own dedicated queue.

## Queues

![](/images/2017/05/active-mq-queue.png)

Queues are the most common messaging pattern implemented by ActiveMQ. 
They provide a direct channel between a producer and a consumer. 
The producer creates messages, while the consumer reads one after another. 
After a message was read, it's gone. 
If multiple consumers are registered for a queue, only one of them will get the message.

**Pros**

- Simple messaging pattern with a transparent communication flow
- Messages can be recovered by putting them back on the queue

**Cons**

- Only one consumer can get a message
- Implies a coupling between producer and consumer as it's a one-to-one relation

## Topics

![](/images/2017/05/active-mq-topic.png)

Topics implement a one-to-many channel between a producer and multiple consumers. 
Unlike a queue, every consumer will receive a message send by the producer.

**Pros**

- Multiple consumers can get a message
- Decoupling between producer and consumers (publish-and-subscribe pattern)

**Cons**

- More complicated communication flow
- A message cannot be recovered for a single listener (all will get it)

## Virtual Topics

![](/images/2017/05/active-mq-virtual-topic.png)

Virtual topics combine both approaches. 
While the producer sends messages to a topic, consumers will receive a copy of the message on their own dedicated queue.

**Pros**

- Multiple consumers can get a message
- Decoupling between producer and consumers (publish-and-subscribe pattern)
- Messages can be recovered by putting them back on the queue

**Cons**

- Might require additional configuration in the broker

## Conclusion

Both, queues and topics, have their own drawbacks. 
While queues tighten producer and consumer, topics lack of an easy way to recover errors of a single consumer. 
Virtual topics provide a solution for both problems. 
Producer and consumers are decoupled by a publish-and-subscribe pattern while error recovery can be done on individual queues.

## More

- [http://activemq.apache.org/virtual-destinations.html](http://activemq.apache.org/virtual-destinations.html)
- [http://activemq.apache.org/what-is-the-difference-between-a-virtual-topic-and-a-composite-destination.html](http://activemq.apache.org/what-is-the-difference-between-a-virtual-topic-and-a-composite-destination.html)
- [http://alvinalexander.com/java/jwarehouse/activemq/activemq-core/src/test/java/org/apache/activemq/broker/virtual/VirtualTopicSelectorTest.java.shtml](http://alvinalexander.com/java/jwarehouse/activemq/activemq-core/src/test/java/org/apache/activemq/broker/virtual/VirtualTopicSelectorTest.java.shtml)

**Best regards,** Thomas.
