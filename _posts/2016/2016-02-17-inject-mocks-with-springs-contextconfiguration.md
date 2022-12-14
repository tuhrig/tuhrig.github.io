---
layout: post
title: "Inject mocks with Spring's @ContextConfiguration"
date: "2016-02-17"
categories: 
  - "coding"
tags: 
  - "java"
  - "spring"
  - "testing"
---

One of the biggest strengths of [Spring](https://spring.io) is its ability to make your code testable. By using **dependency injection** and **inversion of control**, the Spring context defines which objects will be wired into your beans. This makes it easy to wire services, repositories or what ever you like. **But how to wire mocks in your tests?**

# Example

Let's create a simple test setup: we have a service called `OrderService` which handles orders from customers. To do this, the `OrderService` needs a `BillingService`.

@Service
public class OrderService {
    @Autowired
    private BillingService billingService;
    ...
}

@Service
public class BillingService {
    ...
}

A typical test setup would run the Spring context, auto-wire the `OrderService` and test it. But if we would do so, we would also need to include the `BillingService` into the Spring context. And if the `BillingService` would need some other service, we would need to include this too and each and every **transitiv dependency**. Maybe we would end up by loading your whole app just to test one simple service.

# A test setup with mocks

Whenever you want Spring to inject our beans, you must create a **Spring context**. Every bean you want to use must be part of this context as well as every transitiv bean. In this case we need two bean: (1) the `OrderService` which we want to test and the (2) `BillingService` which is a transitiv dependency. Both beans must be part of the Spring context!

However, we don't want to use the real `BillingService` but a mock. To do this, we provide a dedicated `@Configuration` for our test which returns such a mock (with [Mockito](http://mockito.org)). If we would have other dependencies, we would do this for every bean used by the `OrderService`.

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {
        OrderService.class,
        ServiceMockProvider.class
})
public class OrderService Test {
    @Autowired
    private OrderService orderService;
    ...
}

@Configuration
class ServiceMockProvider {
    @Bean
    public BillingService billingService () {
        return  Mockito.mock(BillingService.class);
    }
}

This makes it possible to load a very small Spring context: We only need our class to test and a mock very each of its dependencies. **This is more like an unit test but an integration test** and will run very fast. But the nice thing is, that you can decide which dependencies you want to mock and which not. If you would like to mock the `BillingService` but not the `CustomerService` it fine. You can control the amount of mocks very easily.

**Best regards,** Thomas
