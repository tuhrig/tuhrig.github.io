---
layout: post
title: "Making a Spring bean session scoped"
date: "2015-02-06"
categories: 
  - "coding"
  - "design-pattern"
  - "java"
  - "spring"
---

Spring can not only inject beans (components, services, entities, however you want to call them), it can also inject them according to a certain scope. This is great if you have state-full objects which belongs (for example) to a dedicated user and not to the whole application. To do that, Spring introduced the `@Scope` annotation.

The usage of `@Scope` is straight forward: just annotate your bean with it and specify the scope to use:
    
    @Component
    @Scope(value="session")
    public class MyComponent {
        // ...
    }

The `value` of `@Scope` can be one of `singleton` (one single instance for the whole app), `prototype` (a new instance for every injection), `request` (an instance per web request) or `session` (an instance per web session). See [here](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/Scope.html).

The last two scopes (`request` and `session`) are only available in Spring web applications where the current session is exposed. To do so, you need to add a listener to the `web.xml` which makes the context available:

 org.springframework.web.context.request.RequestContextListener 

If you got both, your annotated bean and the listener in your `web.xml`, you will typically have another problem: **how to use a session scoped bean in a normal (not session scoped) service?**

To solve this problem, Spring provides a proxy mechanism. This means Spring can wire a proxy with the same interface as your actual bean instead of the bean itself. The proxy will not be session scoped (it will be a singleton), but it will fetch the according session scoped bean for each request. This means you can wire your session scoped bean in a not session scoped service and the proxy will do the rest behind the scenes.

To use such a proxy, you can just add it to the `@Scope` annotation like that:

    @Component
    @Scope(value="session", proxyMode = ScopedProxyMode.TARGET\_CLASS)
    public class MyComponent {
        // ...
    }

If you do so, you will discover the next problem soon: **what about your integration tests you made which probably will fail now as they got no session?** The easiest fix for this, is to add the following bean configuration, which adds a session context for each thread (see the [post of Tarun Sapra](https://tarunsapra.wordpress.com/2011/06/28/junit-spring-session-and-request-scope-beans/) for the original description):

**Best regards,** Thomas
