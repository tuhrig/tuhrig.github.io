---
layout: post
title: "Idempotent events with revision numbers"
date: "2019-07-12"
categories: 
  - "coding"
  - "architecture"
  - "design-pattern"
coverImage: "industry-2147403_1280.jpg"
---

In an event-based system (possibly according to [DDD](https://en.wikipedia.org/wiki/Domain-driven_design)) you will be notified about changes. For example, if the price of a product has changed, a `PriceUpdatedEvent` will be thrown. Any system interested in price updates can listen and react to those events.

In most cases the reaction triggered by an event should be idempotent. This means that if we consume the same event twice, the reaction should be triggered only once. We are expecting an _at-least-once delivery_ of events, but not an _exactly-once delivery_.

Imagine a price alert service which sends an e-mail to a customer if a price drops below a certain amount. If the service receives the same `PriceUpdatedEvent` twice, it should not send the e-mail twice.

There are various solutions for this problem:

- Event logs like [Kafka](https://kafka.apache.org) or [Kinesis](https://aws.amazon.com/kinesis) require consumers to keep track of the sequence number they have already processed. So the same sequence number will not be processed twice. However, if a producer sends the same business event again, it will also get a new sequence number.
- Every consumer might evaluate the event and check if it contains actual changes. If an event has already be processed, the changes are already applied. However, if a consumer receives an old event, its data might be different from the current state, but it still should not be applied. because it's old.

The concept of revision numbers is another solution for this problem.

Every event (for example the \`PriceUpdatedEvent\`) gets a revision number. Any time the event is changed, the revision number is increased.

PriceUpdatedEvent: 1€, Rev. 0001
PriceUpdatedEvent: 2€, Rev. 0002
PriceUpdatedEvent: 1€, Rev. 0003

Any consumer can now simply check the revision of the current event against the last processed one. If the revision has increased, the event is new and must be processed. Otherwise it's an old event and can be ignored.

if(event.revision > lastRevision) {
    process(event)
} else { 
    log.info("Event has been ignored - already processed!")
}

If an event is send twice (maybe on purpose), the revision number is still the same.

...
send( PriceUpdatedEvent: 2€, Rev. 0002 )
...
send( PriceUpdatedEvent: 1€, Rev. 0003 )
...
send( PriceUpdatedEvent: 1€, Rev. 0003 )

Note that a message bus like Kafka, Kinesis or ActiveMQ would still assign a new sequence number to every message. It doesn't matter if the event has changed or not - it's a new message from the point of view of the message bus.

A revision number can be designed in different ways:

- A counter stored in the database which is increased (either globally or for each event individually).
- A global service provides increasing revision IDs up on request.
- Based on the timestamp when the modification has occurred.

We decided to use the last option because it doesn't require any infrastructure such as a database or another service. However, if updates can occur simultaneously a transactional database or a global service might be a better fit.

class RevisionGenerator {

        private var last: Long = 0
        private var sequence: Long = 0
        private val mask = 1\_000\_000

        @Synchronized
        fun nextRevision(): Long {
            val current = System.currentTimeMillis()
            if (current == last) {
                sequence++
            } else {
                sequence = 0
                last = current
            }
            return (current \* mask) + sequence
        }
}

**Best regards,** Thomas

# More

- [https://blog.sapiensworks.com/post/2015/08/26/How-To-Ensure-Idempotency](https://blog.sapiensworks.com/post/2015/08/26/How-To-Ensure-Idempotency)
