---
layout: post
title: "Use ranges with ngRepeat"
date: "2016-02-05"
categories: 
  - "coding"
  - "angularjs"
  - "javascript"
---

## ngRepeat

Angular's [ngRepeat](https://docs.angularjs.org/api/ng/directive/ngRepeat) directive provides a simple way to iterate over collections to produce HTML dynamically. 
You probably know it when you have ever worked with lists or arrays in your Angular app:

````angular2html
<ul>
    <li ng-repeat="person in controller.getAllPersons()">
        Name: {{ person.name }}
    </li>
</ul>
````

## Drawbacks

However, there is a small drawback when working with ngRepeat: 
You can only iterate over collections of the current scope. 
Loop definitions (like a typical for-loop) are not supported. 
This means you cannot to something like this:

```html
<!-- Will not work! -->
<li ng-repeat="person in 1..10">...</li>

<!-- Will not work! -->
<li ng-repeat="person in range(1, 10)">...</li>
```

## The solution

But there is a simple trick to build a workaround for this. 
If you use a library like [Lodash.js](https://lodash.com) or [Underscore.js](http://underscorejs.org) the trick becomes even more simple: 
As ngRepeat requires a function on the scope, we just provide such a function!

````javascript
angular.module('myModule').controller('MyController', MyController);

MyController.$inject = ['$scope'];

function MyController($scope) {

    // That's all we need to do! We simply append the Lodash/Underscore library to the current scope
    // (or even to the $rootScope if we like). Now we can use all functions from the library in our
    // partials, e.g. the range(from, to) function!
    $scope._ = _;
}
````

That's all we need. If we append Lodash/Underscore to our scope, we can use the library within our partials. This might looks like this:

````html
<ul>
    <li ng-repeat="n in _.range(1, 11)">{{ n }}</li>
</ul>
````

## Keep in mind

With great power comes great responsibility. 
When we append Lodash/Underscore to the Angular scope, we not only pollute the scope with more functionality, we also put program logic from the controller to the partial. 
And we shouldn't do that! 
A partial should be as easy as possible and all application logic should be in the controller or services. 
But this is also a trade-off, as this solution might be fine for small and simple problems.

**Best regards,** Thomas.
