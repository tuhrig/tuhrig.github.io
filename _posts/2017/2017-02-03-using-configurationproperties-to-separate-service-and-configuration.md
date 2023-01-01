---
layout: post
title: "Using @ConfigurationProperties to separate service and configuration"
date: "2017-02-03"
categories: 
  - "coding"
  - "design-pattern"
  - "java"
  - "spring"
---

Roughly about two years ago, I wrote an article called "[Why using Spring’s @Value annotation is bad](/why-using-springs-value-annotation-is-bad)". 
I described why you should avoid the `@Value` annotation in your code and prefer `@ConfigurationProperties`:

> By using `@Value` in your code, you will scatter the configuration over the application (services, controllers, adapters). 
> Instead of this, configuration should be an encapsulated aspect of the application.
> We want to separate configuration from our actual services.

## The Spring Boot way to do it

[Spring Boot](https://projects.spring.io/spring-boot/) provides an annotation called `@ConfigurationProperties`. 
As you can read right [here](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html), it's exactly designed to separate and externalize configuration. 
That's what we want!

Let's make an example and assume that we want to configure a connection to an external REST service (it could also be a database or whatever). 
We need a configuration including username, password and URL. 
The configuration file (`application.properties`) could look like this:

```yaml
mySuperService.username = admin123
mySuperServcie.password = s3cur3
mySuperService.url = http://some.where.com/api
```

By using Spring Boot's `@ConfigurationProperties` annotation, we would first write a Java class which represents this piece of configuration:

```java
@Component
@EnableConfigurationProperties
@ConfigurationProperties(prefix="mySuperService")
public class MySuperServiceConfig {
    
    @NotNull private String username;
    @NotNull private String password;
    @NotNull private String url;

    // getter and setter...
}
```

As you can see, we have a very obvious matching between the properties file and its Java representation. 
Let's see how we would use this class:

```java
@Service
public class MySuperServiceRestClient {

    private final MySuperServiceConfig config;

    @Autowired 
    public MySuperServiceRestClient(MySuperServiceConfig config) {
        this.config = config;
    }

    // actual code...
}
```

We encapsulated all configuration in a single class (`MySuperServiceConfig`) which we can inject in our service. 
The service doesn't know where the configuration comes from or how the exact properties are called. 
It only knows that it needs an instance of `MySuperServiceConfig` class. 
Separation completed!

## What else can we do?

We have not only just separated our configuration, but we have transformed it into a Java class. 
This means we can just code with it! 
Here some examples:

### Apply default values

We can apply default values for properties which can be overwritten.

```yaml
mySuperService.port = 8080
```

In most cases, the port will be `8080`. 
Only in some rare circumstances, we will have another port - maybe in our test system. 
In this case, we can just apply a default:

```java
@Component
@EnableConfigurationProperties
@ConfigurationProperties(prefix="mySuperService")
public class MySuperServiceConfig {
    
    private String port = "8080"; // default!
    @NotNull private String username;
    @NotNull private String password;
    @NotNull private String url;

    // getter and setter...
}
```

### Convenient methods

Let's assume the following configuration:

```yaml
    mySuperService.port = 8080
    mySuperService.baseUrl = http://some.where.com
    mySuperService.api = api/v1
```

For some reason, we want to be able to configure each property on its own. 
However, in the service we need to have a single url (like `http://some.where.com:8080/api/v1`). 
We can do this in our configuration class!

```java
@Component
@EnableConfigurationProperties
@ConfigurationProperties(prefix="mySuperService")
public class MySuperServiceConfig {
    
    private String port = "8080"; // default!
    @NotNull private String baseUrl;
    @NotNull private String api;

    public String getUrl() {
        return baseUrl + ":" + port + "/" + api;
    }

    // getter and setter...
}
```

### Create different implementations

Our configuration just became a Spring Bean. 
This also means, that we can do all the common Spring things with it! 
For example creating different implementations (aka `@Profile`s).

```java
    public interface MySuperServiceConfig {
        // getter for the configuration properties!
    }

    @Profile("PRODUCTION")
    @Component
    @EnableConfigurationProperties
    @ConfigurationProperties(prefix="mySuperService")
    public class MySuperServiceProductionConfig implements MySuperServiceConfig {
        
        @NotNull private String username;
        @NotNull private String password;
        @NotNull private String url;
    
        // getter and setter...
    }

    @Profile("DEVELOPMENT")
    @Component
    public class MySuperServiceDevelopmentConfig implements MySuperServiceConfig {
        
        // the hard coded test environment for every developer!
        private String username = "testUser";
        private String password = "123456";
        private String url = "http://our.test.server.com";
    
        // getter and setter...
    }

    @Service
    public class MySuperServiceRestClient {
    
        private final MySuperServiceConfig config;
    
        @Autowired 
        public MySuperServiceRestClient(MySuperServiceConfig config) {
            this.config = config;
        }
    
        // actual code...
    }
```

By doing this, we end up with a solution like I've described in "[Why using Spring’s @Value annotation is bad](/why-using-springs-value-annotation-is-bad)". 
The configuration becomes a service which we hide behind an interface. 
We can change how we get the configuration (from a properties file, hard coded or from our configuration server in the cloud) and how we map and process it. 
Our actual services will only contain business logic and will not have to deal with configuration issues.

## More

- [https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)
- [http://www.tothenew.com/blog/binding-external-configuration-properties-in-spring-boot-application](http://www.tothenew.com/blog/binding-external-configuration-properties-in-spring-boot-application)

**Best regards,** Thomas.
