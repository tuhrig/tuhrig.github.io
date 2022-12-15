---
layout: post
title: "AngularJS provider and app configuration"
date: "2016-11-10"
categories: 
  - "coding"
  - "angularjs"
  - "design-pattern"
  - "javascript"
---

AngularJS provides 3 different ways to create a service:

## Factory

A factory is a well known pattern in software development. It aims to create a certain object, in this case a service. In AngularJS this is simply a function which returns an object with methods. In my opinion, this is the cleanest way to create a service in AngularJS.

    angular.module("myApp").factory("MyService", function () {
        function getCreationMethod() {
            return "factory";
        }
        return {
            getCreationMethod: getCreationMethod
        };
    });

## Service

Another way to create a service is a mechanism called "service". In this case, your service is a function which has other functions attached to `this`. It is the shortest way to create a service in AngularJS. However, it sometimes becomes confusing when you need to refer to your own functions using `this`.

    angular.module("myApp").service("MyService", function () {
        this.getCreationMethod = function() {
            return "service";
        };
    });

## Provider

The last way to create a service, is a provider. A provider fulfils a certain interface and implements a method called `$get`. This method returns a function which in turn returns the service object. So by calling `$get` on a provider, you will receive a service factory. This is obviously the most verbose way to create a service.

    angular.module("myApp").provider("MyService", function() {
        this.$get = function() {
            function getCreationMethod() {
                return "provider";
            }
            return {
                getCreationMethod: getCreationMethod
            }
        };
    });

## So why should I use a provider?

So if a provider is the most verbose method you have to create a service, why should you use it at all? To answer this question, it's important to know that no matter which way you use - factory, service or provider - AngularJS will always create a provider for you internally. That's why you will sometimes see an error message like this:

    Error: Unknown provider: MyServiceProvider <- MyService
        at Error (unknown source)

When an AngularJS app is started, it has a certain "boot-order". From the perspective of a developer, it looks like this:

    angular.module('myModule', \[\])
           .config(function(injectables) { // provider & constants
               // do configuration
           })
           .run(function(injectables) { // services & constants
               // do initialization
           });

After you defined your application/module, you have a configuration phase and a run phase. The difference between both phases is the service instantiation. During the `config` phase, the providers have been registered, but they have not been called yet! During the `run` phase all providers have been called and their services have been registered. This means that you cannot inject any service during the `config` phase and that you cannot inject any provider during the `run` phase. Though you can use the `config` phase to configure your providers and it's service.

Let's take a look at the AngularJS UI-Router for an example. If you don't know it, you can find the documentation at [https://github.com/angular-ui/ui-router/wiki/quick-reference](https://github.com/angular-ui/ui-router/wiki/quick-reference).

The AngularJS UI-Router provides both, a provider called `$stateProvider` as well as a service called `$state`. The usage would look like this:

    angular.module("myApp", \["ui.router"\])
           .config(function($stateProvider) {
               // Configure all available states during the config phase  
               // using the $stateProvider!
               $stateProvider.state("home", { ... });
               $stateProvider.state("about", { ... });
               $stateProvider.state("contact", { ... });
           })
           .run(function($state) {
               // Use the configured $state service afters to go to the 
               // initial state! You could do the same in a service.
               $state.go("home");
           });

By using a provider we have the possibility to configure a service before the AngularJS application runs. We can setup things in advance which can be constant during our application lifecycle. If you need something like this, go with a provider. Other wise use a factory or service.

## More

- [https://docs.angularjs.org/guide/module#!%2F](https://docs.angularjs.org/guide/module#!%2F)
- [http://www.angularjshub.com/examples/modules/configurationrunphases](http://www.angularjshub.com/examples/modules/configurationrunphases)

**Best regards,** Thomas.
