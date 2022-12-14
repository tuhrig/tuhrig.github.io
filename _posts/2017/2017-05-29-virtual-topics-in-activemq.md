---
layout: post
title: "Virtual Topics in ActiveMQ"
date: "2017-05-29"
categories: 
  - "coding"
tags: 
  - "activemq"
  - "design-pattern"
  - "java"
  - "jms"
  - "spring"
  - "spring-boot"
coverImage: "square-dancing-934208_1280.jpg"
---

A couple of days ago I published [a post](http://tuhrig.de/queues-vs-topics-vs-virtual-topics-in-activemq/) about the difference between queues, topics and virtual topics. Today I want to share some practical information on how to use virtual topics in [ActiveMQ](http://activemq.apache.org/) with [Spring Boot](https://projects.spring.io/spring-boot/).

# Virtual Topics

[![](images/Screen-Shot-2017-05-22-at-09.06.04.png)](http://tuhrig.de/wp-content/uploads/2017/05/Screen-Shot-2017-05-22-at-09.06.04.png)

Virtual topics are a combination of topics and queues. Producers will write messages to a topic while listeners will consume from their own queue. ActiveMQ will copy and duplicate each message from the topic to the actual consumer queues.

This combination of topics and queues has some advantages over conventional topics:

- Even if a consumer is offline, no messages will be lost. ActiveMQ will copy all messages to the registered queues. As soon as a consumer comes back online, he can handle the messages stored on his queue.
- If a consumer cannot handle a message, it will put on a dead letter queue. The consumer can be fixed and the message can be put back on his own dedicated queue without affecting the other consumer (as a topic would do).
- We can register multiple instances of a consumer on a queue to implement a load balancing mechanism.

# Using virtual topics with Spring Boot

Using virtual topics with Spring Boot is very straight forward. The configuration is based on conventions to which we will stick in the first part. However, you can easily change the conventions as we will see below.

## Producer

From the point of view of the producer, virtual topics look exactly like normal topics. The only difference is the name of the topic which must have the syntax `VirtualTopic.<name>` by convention.

@Autowired
private JmsTemplate jmsTemplate;

// ...

jmsTemplate.convertAndSend(new ActiveMQTopic("VirtualTopic.MY-SUPER-TOPIC"), myMessage);

## Consumer

On the side of the consumer, we find a similar naming convention. The destination must match the syntax `Consumer.<name>.VirtualTopic.<name>` by convention.

@JmsListener(destination = "Consumer.myConsumer1.VirtualTopic.MY-SUPER-TOPIC")
public void receive(MyMessage myMessage) {
    // do something...        
}

@JmsListener(destination = "Consumer.myConsumer2.VirtualTopic.MY-SUPER-TOPIC")
public void receive(MyMessage myMessage) {
    // do something else...        
}

## Conventions

By following these two simple naming conventions, ActiveMQ will automatically do all the magic in the background. Every message send to the virtual topic will be copied to each of the individual consumer queues.

Producer writes to: `VirtualTopic.<name>` Consumer reads from: `Consumer.<name>.VirtualTopic.<name>`

**Note** that you need to explicitly state in you Java code that the destination of the producer is a topic (not a queue) by using `new ActiveMQTopic(...)` when sending the message.

[![](images/Screen-Shot-2017-05-29-at-17.04.23-1024x122.png)](http://tuhrig.de/wp-content/uploads/2017/05/Screen-Shot-2017-05-29-at-17.04.23.png)

[![](images/Screen-Shot-2017-05-29-at-17.03.55-1024x113.png)](http://tuhrig.de/wp-content/uploads/2017/05/Screen-Shot-2017-05-29-at-17.03.55.png)

# Advanced configuration

The naming conventions above can be changed in ActiveMQ. To do this, we need to modify the `activemq.xml`. This means we cannot do this in our Java code - we must do it outside in our broker configuration! The configuration goes like this:

This small piece of configuration allows you to change the naming convention of your virtual queues. It also allows you to turn on the selector awareness which is de-activated by default.

Read more at [http://activemq.apache.org/virtual-destinations.html](http://activemq.apache.org/virtual-destinations.html).

**Best regards,** Thomas
