---
layout: post
title: "Expression based security with Spring Security"
date: "2016-05-18"
categories: 
  - "coding"
tags: 
  - "java"
  - "security"
  - "spring"
---

Many web apps have a very simple security concept based on _user roles_. You might have some admin, some common users and maybe some more roles like a moderator or a super-user. Such a role concept can be easily implemented with [Spring Security](http://projects.spring.io/spring-security/). For example, you could secure your app based on routes which are available for certain roles:

http.authorizeRequests()
    .antMatchers("/admin/\*\*").access("hasRole('ROLE\_ADMIN')")
    .antMatchers("/user/\*\*").access("hasRole('ROLE\_ADMIN') or hasRole('ROLE\_USER')")
    .and()
    .formLogin();

If you want to be more flexible, you could also annotate your controllers instead of making a central configuration. While a central security configuration can become quite complex if you have a lot of routes with different security rules, using the `@PreAuthorize` annotation is very simple. All security constraints are right at the method where they apply.

@PreAuthorize("hasRole('ROLE\_ADMIN')")
@RequestMapping(value = "/admin/info", method = RequestMethod.GET)
public InfoDto getAdminInformation() {
    // ...
}

# But what if your security model has more constraints than roles?

Spring Security makes it easy to handle roles. But there are a lot of situations where roles are not enough. Imagine an order system (something like Amazon) where an user can cancel an order. To do this, you would introduce an user role which will have the permission to cancel an order. So anonymous users will not be allowed to cancel an order. That’s good, but only half of the story! There is one more (maybe the most important) security constraint: An user can only cancel those orders which belongs to him! And not orders of somebody else!

Such a rule is a typical business rule which can be handled by a service. The service might load all orders which belongs to a customer from the database, iterate over them and check if the given order ID is contained in the list. This could look like this:

@Service
public class OrderService {
 
    private final OrderRepository orderRepository;
 
    @Autowired
    public OrderService(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }
 
    public boolean ownsOrder(final String orderId) {
 
        final User user = userService.getCurrentUser();
        final List orders = orderRepository.getOrders(user);
 
        // If we found an order which has the given order ID in the orders
        // of the user, we know that the order belongs to the user!
        return contains(orders, orderId);
    }
} 

If we have such a service which simply returns true or false we can easily use it together with Spring Security:

@RestController
@RequestMapping("/api/orders")
public class OrderController {
 
    ...
 
    @PreAuthorize("@orderService.ownsOrder(#orderId)")
    @RequestMapping(method = GET, path = "/{orderId}")
    public getOrder(@PathVariable String orderId) {
        ...
    }
}

By using the `@PreAuthorize` annotation, you can use any Spring Bean. In the example above, we use the OrderService: `@orderService.ownsOrder(#orderId)`. We pass the input parameter directly from the request mapping. The service simply returns true or false and Spring will either allow or forbid the access to the controller method.

It’s also possible to combine those rules:

@PreAuthorize("hasRole('ADMIN') or (hasRole('USER') and @orderService.ownsOrder(#orderId))")

This makes it possible to build complex security expressions which can use roles as well as Spring beans. By using Spring beans you can implement all types of security checks.

**Best regards,** Thomas

# More

- [https://docs.spring.io/spring-security/site/docs/3.0.x/reference/el-access.html](https://docs.spring.io/spring-security/site/docs/3.0.x/reference/el-access.html)
- [https://www.isostech.com/blogs/spring-2/spring-preauthorize-spel-examples/](https://www.isostech.com/blogs/spring-2/spring-preauthorize-spel-examples/)
- [http://blog.solidcraft.eu/2011/03/spring-security-by-example-securing.html](http://blog.solidcraft.eu/2011/03/spring-security-by-example-securing.html)
- [http://stackoverflow.com/questions/6632982/how-to-create-custom-methods-for-use-in-spring-security-expression-language-anno](http://stackoverflow.com/questions/6632982/how-to-create-custom-methods-for-use-in-spring-security-expression-language-anno)
