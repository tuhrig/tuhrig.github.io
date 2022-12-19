---
layout: post
title: "My Logging Best Practices"
date: "2020-01-16"
categories: 
  - "coding"
---

If you are a backend developer like me, logging is the window to your application. 
Unlike in the frontend, there's not much to see except of your log messages. 
Here are some of my personal guidelines I use when I write logs.

## Log after, not before

Back in the days, a logbook was written on every ship. 
It was like a diary which recorded important events throughout the day. 
And just like a traditional logbook, we should log things that had happened instead of things we are going to do.

Example:

```java
// don't do that
log.info("Making request to REST API")
restClient.makeRequest()

// do that
restClient.makeRequest()
log.info("Made request to REST API")
```

The first log statement doesn't tell much. 
When reading it, you will not know if the REST call was successful or not. 
To do so you must look for the absence of an exception. 
And if you read this log but miss the subsequent exception you will be confused for the rest of the day (trust me).

The second log is much better. 
It clearly states that the operation was successful. 
If the REST call had failed, you would not see this log - there would be an exception instead.

I apply this rule to all `INFO` logs. 
However, I make exceptions for `DEBUG`.

## Separate parameters and messages

A typical log message contains two types of data. 
One type is a handwritten message which states was is going on. 
The second type is a list of (technical) parameters involved in the operation. 
You should try to separate both parts.

``` java
// don't do that
restClient.makeRequest()
log.info("Made request to {} on REST API.", url)

// do that
restClient.makeRequest()
log.info("Made request to REST API. [url={}]", url)
```

The first log message has some flaws. 
It's difficult to parse for example for [Grok patterns](https://logz.io/blog/logstash-grok/). 
So it becomes harder to extract IDs or parameters in our logging tool. 
It's also difficult to read. 
Imagine having a very long URL possibly with a list of parameters at their end. 
Half of the log message might be out of your screen. 
And of course, the message is more difficult to extend. 
If you want to add another parameter (such as the HTTP method used) you must rewrite your whole sentence (which might break log parsing).

The seconds version has none of these flaws. 
It's easy to parse because the parameter list has a clear syntax (`[key=value, key=value...]`). 
It's easy to read, as you can see the sentence right up-front. 
And it's easy to extend as you can just add another parameter to the list.

## Distinguish between WARNING and ERROR

Obviously, log levels are there for a reason. 
You should use them appropriately. 
And there are some key differences between a `WARNING` and an `ERROR`.

If you did some operations which actually worked, but there have been some issues - that's a `WARNING`. 
But if you did some operation and it simply didn't work - that's an `ERROR`.

Let's look at an example:

``` java
try {
    restClient.makeRequest()
    log.info("Made request to REST API. [url={}]", url)
} catch(e: UnauthorizedException) {
    log.warn("Request to REST API was rejected because user is unauthorized. [url={}, result={}]", url, result)
} catch(e: Exception) {
    log.error("Request to REST API failed. [url={}, exception={}]", url, exception)
}
```

The REST call might have one of three outcomes:

- It can work like a charm. That's an `INFO` (_after_ the call).
- It can fail with an unexpected exception. That's an `ERROR` (_instead_ of the `INFO` log).
- It can result in some expected exception (in this case `UnauthorizedException`). That's a `WARNING`.

In case of a `WARNING`, you did something, but you didn't do it perfectly. 
In case of an `ERROR` you didn't do it.

Also note that a `WARNING` (and also an `ERROR` of course) is a call to action. 
If nobody needs to react and to do something, then you don't need to log a `WARNING`.
But this also means, that you don't log a `WARNING` if this particular case is expected to happen. 
For example, if you search for something and don't find it, this is expected behaviour most of the time.
It's neither a `WARNING` nor an `ERROR`. 

## INFO is for business, DEBUG for technology

The INFO log should look like a book. 
It should tell you what had happened, not necessarily how. 
This means that INFO is better suited for business-like log messages compared to technical stuff. 
Technical related messages should (usually) be `DEBUG`.

```
INFO  | User registered for newsletter. [user="Thomas", email="thomas@tuhrig.de"]
INFO  | Newsletter send to user. [user="Thomas"]
INFO  | User unsubscribed from newsletter. [user="Thomas", email="thomas@tuhrig.de"]
```

This type of log tells you a story from the point of view of our business. 

Now what are technical logs?

```
DEBUG | Saved user to newsletter list. [user="Thomas", email="thomas@tuhrig.de"]
DEBUG | Send welcome mail. [user="Thomas", email="thomas@tuhrig.de"]
INFO  | User registered for newsletter. [user="Thomas", email="thomas@tuhrig.de"]
DEBUG | Started cron job to send newsletter of the day. [subscribers=24332]
INFO  | Newsletter send to user. [user="Thomas"]
INFO  | User unsubscribed from newsletter. [user="Thomas", email="thomas@tuhrig.de"]
```

Every (business) use-case results in a single line of `INFO` log. 
Additionally, there are `DEBUG` logs which give a more detailed insight in how the process works.

## Much more

Of course, there's much more to do for good logs. 
You also need to consider things like **tracing**, **log aggregation** and **metrics**. 
But when it comes down to the pure writing, I really recommend those little rules.

**Best regards,** Thomas.
