---
layout: post
title: "Self-made event dispatcher"
date: "2018-03-15"
categories: 
  - "coding"
  - "java"
  - "architecture"
---

In my current project, we are using an event-based architecture in order to communicate between services. 
The events are implemented as [JMS](https://www.baeldung.com/spring-jms) messages, which we send over [ActiveMQ](http://activemq.apache.org). 
Each system sends and listens to multiple events. 
While sending events is pretty easy, on the listener side we quickly ended up with a class explosion:

![](/images/2018/03/jms-listeners.png)

A typical listener would look like this:

```java
@Component
@AllArgsConstructor
public class MyEventListener {

    private SomeService someService;

    @JmsListener(destination="ActiveMQ-Event-Topic", selector="_type='MyEvent'")
    public void onEvent(MyEvent myEvent) {
        someService.handle(myEvent);
    }
}
```

So we came up with a simple idea to make this kind of boilerplate code superfluous.

## Event handler annotation

Our first step was to create a custom annotation to mark event handler methods. 
It looks like this:

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface EventHandler {

}
```

We placed this annotation on service methods which should become event handlers. 
In case of the `SomeService` from the example above, it looks like this:

```java
@Service
public class SomeService {

    @EventHandler
    public void handle(MyEvent myEvent) {
        // ...
    }
}
```

The annotation comes with two conventions:

- It can only be placed on methods of Spring beans - not on unmanaged classes!
- The method must have exactly one single parameter: the event to handle!

## Event handler registry

In the next step, we created an event handler registry in order to pick up all event handlers annotated with `@EventHandler`. 
By using some functionality of Spring, it wasn't too much work or magic to do this. 
Our registry looks like this:

```java
@Component
public class SpringEventHandlerRegistry implements ApplicationListener {

    private final List eventHandlerProxies = new ArrayList<>();

    @Override
    public void onApplicationEvent(ApplicationReadyEvent applicationReadyEvent) {
        ApplicationContext applicationContext = applicationReadyEvent.getApplicationContext();
        String[] beanDefinitionNames = applicationContext.getBeanDefinitionNames();
        for (String beanDefinitionName: beanDefinitionNames) {
            Object bean = applicationContext.getBean(beanDefinitionName);
            Method[] methods = bean.getClass().getMethods();
            for (Method method : methods) {
                if (method.isAnnotationPresent(EventHandler.class)) {
                    Class[] parameterTypes = method.getParameterTypes();
                    Assert.state(parameterTypes.length == 1, "Event handler must have exactly one parameter!");
                    Class parameterType = parameterTypes[0]; // one and only!
                    EventHandlerProxy handlerProxy = new EventHandlerProxy(parameterType, bean, method);
                    eventHandlerProxies.add(handlerProxy);
                }
            }
        }
    }
    
    public List getEventHandlers(String acceptedType) {
        return eventHandlerProxies
                .stream()
                .filter(eventHandler -> eventHandler.accepts(acceptedType))
                .collect(toList());
    }
}
```

**So what does this class do?** 
First, it implements the `ApplicationListener` interface and listens for an `ApplicationReadyEvent`. 
This means, that it is called as soon as the application is ready (== _after the boot process_). 
It will then look for any Spring bean which has a method annotated with our `@EventHandler` annotation. 
Each method will be wrapped in a `EventHandlerProxy` which is stored in a list. 
The `EventHandlerProxy` encapsulates the event handler method in a convenient object:

```java
@AllArgsConstructor
public class EventHandlerProxy {

    private final Class eventClass;
    private final Object target;
    private final Method method;

    public String getType() {
        return eventClass.getSimpleName(); // e.g. "MyEvent" for de.tuhrig.MyEvent.java
    }

    public boolean accepts(String type) {
        return getType().equals(type);
    }

    public Class getEventClass() {
        return eventClass;
    }

    public void invoke(Object event) {
        ReflectionUtils.invokeMethod(method, target, event);
    }
}
```

## Event dispatcher

The last piece which is missing is the event dispatcher itself. 
The dispatcher is a JMS listener which will listen to all events and invoke any internal event handler which accepts it. 
To do so, it will ask the event handler registry for all registered handlers for the given event.

```java
@Component
@AllArgsConstructor
public class EventListener  {

    private EventHandlerRegistry eventHandlerRegistry;
    private ObjectMapper objectMapper;

    @SneakyThrows
    @JmsListener(destination = "ActiveMQ-Event-Topic")
    public void onEvent(TextMessage textMessage) {
        String type = textMessage.getStringProperty("_type");
        eventHandlerRegistry
                .getEventHandlers(type)
                .forEach(eventHandler -> {
                    String json = textMessage.getText();
                    Object event = objectMapper.readValue(json, messageClass.getMessageClass());
                    eventHandler.invoke(event);
                });
    }
}
```

## Notes

An important detail is the `_type` property which is expected on every event. 
This property tells us the type (== Java class) of the event. 
For example, whether it is an `MyEvent.java` or a `YourEvent.java`. 
We need this information in order to map the event (which is plain `JSON`) to a Java object. 
So whenever we send an event we set this property:

```java
jmsTemplate.convertAndSend("ActiveMQ-Event-Topic", jsonPayload, message -> {
    message.setStringProperty("_type", "MyEvent"); // for example
    return message;
});
```

**Best regards,** Thomas.
