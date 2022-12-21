---
layout: post
title: "Why using Spring's @Value annotation is bad"
date: "2015-03-11"
categories: 
  - "coding"
  - "design-pattern"
  - "java"
  - "spring"
---

# @Value

Configuration is an important topic of every application which has more than a couple of hundred lines of code. In case you are using Spring, you would typically use Spring's `@Value` annotation to load values from a Java properties file. This could look like this:

@Service
public class MyService {

    @Value("${my.config.property.key}")
    private String someConfigValue;
    
    //...
}

If you do so, Spring would inject the value for the key `my.config.property.key` from your properties file right into your class and you are done. Easy! Maybe too easy...

# What will happen...

What will happen when you use this technique through your application? Well, whenever you need some property, you will inject it. You will inject it in services, controllers and components and you will properly inject the same property in different classes. That's easy and absolutely what the mechanism is about.

However, this means nothing else than scattering your configuration about your whole application. Every class can pick a couple of properties to use. You will have no idea or control which class is using which properties. You will end-up with doing a full text search on your project to find out where a single key is used. You will have a lot of fun if you want to rename one of those keys or when you need to set each and every property for each and every class in your unit tests.

# Configuration is an service

Instead of this, **configuration should be an encapsulated service** of your application such as any other functionality, too. You encapsulate persistence in DAOs, you encapsulate REST services in controllers and you encapsulate security in authenticators. So why not encapsulating configuration?

If you do so, you will have a single point of responsibility to load and get your configuration from. As any other service, you can easily change the implementation. Maybe you don't want to load your properties from a properties file, but from a database or web service! If you encapsulate your configuration in a service, that's not a big deal. You just change the implementation. You can also write some unit tests for your configuration (Why not? Configuration can become complicated when properties are combined or a certain configuration is determined based on a couple of other properties!) or you can do sanity checks for properties in the configuration service. You can do logging, you can do security (Maybe the path to the password file shouldn't be visible everywhere, right?), you can do caching, you can do... well, you get the point.

@Service
public class ConfigurationService {

    @Value("${my.config.property.key}")
    private String someConfigValue;

    // getters, setters, sanity checks and so on...
}

@Service
public class MyService {

    @Autowire
    private ConfigurationService configurationService ;
    
    //...
}

# Or even better

If you are using [Spring Boot](https://spring.io/projects/spring-boot) (not only Spring), you don't need to re-invent the wheel. Spring Boot provides an elegant way to separate configuration and from the rest of your application: [@ConfigurationProperties](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/context/properties/ConfigurationProperties.html). By using Spring Boot's @ConfigurationProperties you can easily encapsulate your configuration in a separate class and avoid scattering direct dependencies to your configuration files all over your code. Reade more [here](https://tuhrig.de/using-configurationproperties-to-separate-service-and-configuration).

**Best regards,** Thomas
