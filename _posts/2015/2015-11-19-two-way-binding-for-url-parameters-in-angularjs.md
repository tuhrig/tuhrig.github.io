---
layout: post
title: "Two-way binding for URL parameters in AngularJS"
date: "2015-11-19"
categories: 
  - "coding"
  - "angularjs"
  - "html"
  - "javascript"
---

URL parameters are an essential part of single page applications. 
They hold the current state of an application and enable users to open the application in a certain state. 
Here's an example:

```
http://localhost:8080/index.html#/page?showAll=true&filterByName=Thomas
                                |                                     |
                                +--------- for on the client ---------+
                                             (anchor part)
```

In this example, `showAll=true` and `filterByName=Thomas` are URL parameters. 
The hash (`#`) divides the URL into two parts: 
a part which goes to the server (before `#`) and a part which stays on the client (behind `#`). 
This is the so called _anchor part_ which holds our URL parameters. 
This part of the URL is purely for our AngularJS client!

## Two-way binding

Two-way binding means that we bind a parameter of our URL (e.g. `filterByName`) to a parameter of the controller (let's say it has the same name: `myController.filterByName`). 
To achieve this, we need to update our controller if the URL parameter changes and we need to update our URL parameter if the controller changes vice versa. 
That's what two-way mean - controller to URL, URL to controller.

## Controller >> URL

Let's start with the first way: controller to URL. 
Everytime a property of the controller changes, we want to update the corresponding property of the URL. 
To do so, we use Angular's `$watch` method (see [here](https://docs.angularjs.org/api/ng/type/$rootScope.Scope)). 
The method `$watch` allows us to watch a value and to do something if it changes. 
Exactly what we want! 
The code is pretty straight forward:

```javascript
angular
    .module('my.module')
    .controller('MyController', function($scope, $location) {
        var self = this;
        var filterByName = null; // optional declaration!
        // Controller to URL
        $scope.$watch(function() { return self["filterByName"] }, function (newVal) {
            console.log("Property changed!");
            $location.search("filterByName", newVal);
        });
    });
```

This code will do the following: 
It will create a watcher which listens on the property `filterByName` of our controller. 
If the property changes, we get the new value and set it to the URL (by `$location.search("filterByName", newVal);`).
To use the URL parameters we use a service called `$location` provided by Angular. 
That's the default way to access the URL.

## URL >> controller

Now we only need the way back: URL to controller. 
We can use another watcher for that. 
But in this case we use Angular's `$on`. 
This looks like that:

```javascript
angular
    .module('my.module')
    .controller('MyController', function($scope, $location) {
        var self = this;
        var filterByName = null; // optional declaration!
        // Controller to URL
        $scope.$watch(function() { return self["filterByName"] }, function (newVal) {
            console.log("Property changed!");
            $location.search("filterByName", newVal);
        });
        // URL to controller
        $scope.$on('$locationChangeSuccess', function(event) {
            console.log("URL changed!");
            self["filterByName"] = $location.search()["filterByName"];
        });
    });
```

What `$on` does is simple: 
It listens for events, e.g. the `$locationChangeSuccess` event, which is thrown after the URL has changed. 
If this happens, we use the `$location` service to read the value from the URL and assign it to our controller.

## Put it to a method!

We already have achieved what we wanted: we have a two-way binding between our controller and the URL. 
One last thing we might do right now, is to put this into a nice method we can reuse:

```javascript
angular
    .module('my.module')
    .controller('MyController', function($scope, $location) {
        var self = this;
        var filterByName = null; // optional declaration!
        bind("filterByName");
        function bind(valueName) {
            // Controller to URL
            $scope.$watch(function() { return self[valueName] }, function (newVal) {
                console.log("Property changed!");
                $location.search(valueName, newVal);
            });
            // URL to controller
            $scope.$on('$locationChangeSuccess', function(event) {
                console.log("URL changed!");
                self[valueName] = $location.search()[valueName];
            });
        }
    });
```

If the property of our controller changes (e.g. because the user enters data) the URL will change. 
And if the URL changes (e.g. because the user clicked on a link) the controller will change. 
We have a two-way binding between controller and URL!

## More

- [Angular's $watch and $on](https://docs.angularjs.org/api/ng/type/$rootScope.Scope)
- [Angular's $location](https://docs.angularjs.org/api/ng/service/$location)

**Best regards,** Thomas.
