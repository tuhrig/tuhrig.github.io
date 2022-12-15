---
layout: post
title: "Booting an AngularJS application"
date: "2016-10-20"
categories: 
  - "coding"
  - "angularjs"
  - "html"
  - "javascript"
---

Every back-end application has a boot process. Services must be instantiated, connections must be established and configurations must be loaded before the application is ready. **How can we do something similar for an AngularJS application?**

## ng-app

The typical way to start an AngularJS application is to use the `ng-app` directive on an HTML element (mostly `body`). Using `ng-app` Angular will do the bootstrapping of your application automatically. This is perfectly fine for most cases. But if we need more controller of the bootstrap process of Angular, we can do thins manually.

## Manual start-up

Instead of using `ng-app` we can write a piece of JavaScript code to start our application:

    function startApplication() {
        angular.element(document).ready(function() {
            angular.bootstrap(document, \["myApp"\]);
        }
    }

    startApplication();

Now we are able to start our application manually and to do initialization tasks!

## Task 1: Loading data from the backend

A typical start-up task would be to load some initial data (for example a configuration) from the back-end. The code below will make a `GET`\-call to the back-end in order to retrieve some configuration. After the `GET`\-call has finished the application will be started. This makes sure that we have all data up-front.

    var app = angular.module("myApp");
    
    function loadPropertiesFromBackend() {
        var ngInjector = angular.injector(\["ng"\]);
        var $http = ngInjector.get("$http");
    
        return $http.get("config/from/server").then(function(response) {
            app.constant("config", response.data);
        });
    }

    function startApplication() {
        angular.element(document).ready(function() {
            angular.bootstrap(document, \["myApp"\]);
        }
    }

loadPropertiesFromBackend().then(startApplication);

## Task 2: Going to an initial state

Most AngularJS applications have different states. E.g. a view of the user profile (`/app/user`), a view of the private messages (`/app/messages`) or simply a home screen (`/app/home`). Before we start our application, we want to navigate to the correct state. While there are many ways to achieve this, here is an approach how to do it by using an bootstrap script:

The method `angular.bootstrap()` will return the injector of our application. We can use this injector to access all services of our application (including the routing). In the example below, we assume that we save every state in the session storage of the browser. So we can always look in the session storage to finde the last state where the user has been. If the user now reloads the page in his browser, the AngularJS application will start again. The application will look in the session storage, find the `LAST_SAVED_STATE` and go to it. Note that we could also use another type of storage (maybe cookies) to persist the state.

    var app = angular.module("myApp");

    function loadPropertiesFromBackend() {
        var ngInjector = angular.injector(\["ng"\]);
        var $http = ngInjector.get("$http");
    
        return $http.get("config/from/server").then(function(response) {
            app.constant("config", response.data);
        });
    }

    function startApplication() {
        angular.element(document).ready(function() {
            var appInjector = angular.bootstrap(document, \["myApp"\]);
    
            var $state = injector.get("$state");
            var $window = injector.get("$window");
    
            var uiRouterState = $window.sessionStorage.getItem('LAST\_SAVED\_STATE');
            if(!!uiRouterState && "undefined" !== uiRouterState) {
                $state.go(uiRouterState);
            } else {
                $state.go("HOME\_SCREEN");
            }
        }
    }

loadPropertiesFromBackend().then(startApplication);

## Where to put the start-up script to

We usually create one start-script per application, e.g. `my.app.bootstrap.js`. We code shown above is wrapped into an IIFE and included as the last dependency into our HTML code:

        ...
        

<script src="my.app.bootstrap.js" type="text/javascript"></script>

## More

- [https://blog.mariusschulz.com/2014/10/22/asynchronously-bootstrapping-angularjs-applications-with-server-side-data](https://blog.mariusschulz.com/2014/10/22/asynchronously-bootstrapping-angularjs-applications-with-server-side-data)
- [https://docs.angularjs.org/api/ng/directive/ngApp](https://docs.angularjs.org/api/ng/directive/ngApp)
- [https://docs.angularjs.org/guide/bootstrap](https://docs.angularjs.org/guide/bootstrap)

**Best regards,** Thomas.