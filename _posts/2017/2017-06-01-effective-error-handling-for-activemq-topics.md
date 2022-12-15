---
layout: post
title: "Effective error handling for ActiveMQ topics"
date: "2017-06-01"
categories: 
  - "coding"
  - "activemq"
  - "design-pattern"
  - "java"
---

No matter what type of software you make, there will always be errors. There might be bugs in your code or an external system is just down at the moment. No matter what it is, it will cause you trouble.

Asynchronous messaging (with [JMS](https://en.wikipedia.org/wiki/Java_Message_Service)) might help to deal with such situations. Especially when you depend on external systems. Instead of making a synchronous call (like a REST call), you just send a message to a queue. As soon as the message is out, the transaction management will assure that it is consumed successfully. Otherwise an error handling process will kick in.

In the following I want to give an example on how we deal with errors in our project with [ActiveMQ](http://activemq.apache.org).

## Our system

Our system is composed of multiple _microservices_. Those services are [event-driven](https://martinfowler.com/articles/201701-event-driven.html) and communicate with messages send over ActiveMQ. Since a single event is of interest for more then one system, we decided to use [topics instead of queues](http://activemq.apache.org/how-does-a-queue-compare-to-a-topic.html). Since all parts of our systems belongs together, we use a single topics with typed messages. This means all events will be written to the same topic and all listeners will consume from that topic. However, listeners will [filter for certain message types](http://activemq.apache.org/selectors.html) which they use (instead of getting every message which comes around).

[![](images/example-topic-system-1.png)](http://tuhrig.de/wp-content/uploads/2017/05/example-topic-system-1.png)

## Virtual Topics

The first action we took to improve our error handling was switching from normal topics to [virtual topics](http://activemq.apache.org/virtual-destinations.html). In contrast to normal topics, virtual topics will dispatch messages to physical queues - one for each consumer. This allows us to use topics but to have an individual error handling for every consumer. Another benefit is that consumer will become persistent. This means, even if a consumer is offline, the message will be delivered to its queue. When the consumer comes back online, it can consume all (old) messages. Nothing will be lost.

[![](images/Screen-Shot-2017-05-22-at-09.06.04.png)](http://tuhrig.de/wp-content/uploads/2017/05/Screen-Shot-2017-05-22-at-09.06.04.png)

Read more in one of my previous posts:

- [http://tuhrig.de/virtual-topics-in-activemq](http://tuhrig.de/virtual-topics-in-activemq) (how to configure everything)
- [http://tuhrig.de/queues-vs-topics-vs-virtual-topics-in-activemq](http://tuhrig.de/queues-vs-topics-vs-virtual-topics-in-activemq) (more background information)

## Redelivery Policy

Our second step was to think about a [redelivery policy](http://activemq.apache.org/redelivery-policy.html). A redelivery policy comes into play as soon as the delivery of a message fails. ActiveMQ will automatically try to deliver the message again. The number of retries as well as the time period between every redelivery can be configured.

However, we came to the conclusion to stick with ActiveMQ's default settings. The only change we made was to set the queue name (as shown below). This assures that if the message cannot be delivered finally, that the queue name is shown in AcitveMQ's web interface.

    @Bean
    public RedeliveryPolicy redeliveryPolicy() {
        RedeliveryPolicy topicPolicy = new RedeliveryPolicy();
        topicPolicy.setQueue("Consumer.myConsumer.VirtualTopic.MY\_TOPIC");
        return topicPolicy;
    }

## Dead Letter Queues

If a message cannot be (re-) delivered finally, ActiveMQ will put it on a [dead letter queue](http://activemq.apache.org/message-redelivery-and-dlq-handling.html). This means that the message is moved to a special place where we can manually deal with the error. We can look into the message, analyse the bug and fix it. Afterwards, the message can be put back on the regular queue of the failed consumer. As we use virtual topics, this will not affect the other consumer as each of them has its own physical queue.

By default, ActiveMQ will put all failed messages to a single dead letter queue. This makes it hard to see which consumers have failed. Because of that, we decided to use [individual dead letter queues](http://activemq.apache.org/message-redelivery-and-dlq-handling.html) for each consumer. Failing consumers will be easy to spot in ActiveMQ's web console.

[![](images/Screen-Shot-2017-06-01-at-09.38.05-1024x408.png)](http://tuhrig.de/wp-content/uploads/2017/06/Screen-Shot-2017-06-01-at-09.38.05.png)

We used the following configuration in our `activemq.xml`:

## Conclusion

By applying three simple techniques, we build an effective error handling for our event-driven application with ActiveMQ.

- **Virtual topics** provide an individual error handling for consumer as well as persistent delivery.
- **Redelivery policies** will automatically give consumers a second chance.
- **Individual dead letter queues** make problems easy to spot and messages easy to recover.

[![](images/Screen-Shot-2017-06-01-at-09.46.00-1024x536.png)](http://tuhrig.de/wp-content/uploads/2017/06/Screen-Shot-2017-06-01-at-09.46.00.png)

## Read more

My previous posts:

- [Configure virtual topics in ActiveMQ](http://tuhrig.de/virtual-topics-in-activemq)
- [Comparison between queues, topics and virtual topics](http://tuhrig.de/queues-vs-topics-vs-virtual-topics-in-activemq)

ActiveMQ documentation:

- [Message Redelivery and DLQ Handling](http://activemq.apache.org/message-redelivery-and-dlq-handling.html)
- [Virtual Destinations](http://activemq.apache.org/virtual-destinations.html)

**Best regards,** Thomas.
