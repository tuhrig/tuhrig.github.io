---
layout: post
title: "Testing AngularJS directives with Jasmine"
date: "2016-01-20"
categories: 
  - "coding"
tags: 
  - "angularjs"
  - "javascript"
  - "testing"
---

# Directives

AngularJS provides a mechanism called [directives](https://docs.angularjs.org/guide/directive). Directives are markers in our HTML which Angular will read and use to inject some special behavior to our HTML code. As this sounds a little bit theoretically, let's look at an example:

Enter your name: 

In this example the directive `ng-model` is used which will bind the value of the input field to a field called `name` in the current `$scope`. This directive already comes with AngularJS, but it's also possible to write your own directive. That's what we do right now.

# A custom directive

In order to have an useful example to test, we write our own directive. In this case we will use a directive which will listen for an "on-enter-event" on an element. It looks like that:

angular
    .module('my.module')
    .directive('onEnter', function () {
        return function (scope, element, attrs) {
            element.bind("keydown keypress", function (event) {
                if(event.which === 13) {
                    scope.$apply(function (){
                        scope.$eval(attrs.onEnter);
                    });
                    event.preventDefault();
                }
            });
        };
    });

And you use it like that:

Enter your name: 

The example creates a directive called `onEnter` **which you must refer as `on-enter` in your HTML**. The directive takes a single argument which is a function in the current scope (e.g.: `showNotification()`). So when we type in your input field and press enter the directive will be called. It will get an event and it will check if the event comes from "enter" or another key. If it was an "enter", the directive will call our method. Looks good? So let's test it.

# Testing

Testing this directive isn't a big thing, but it will bring us the evidence that it works like we expect it. A Jasmine test might look like this:

describe('onEnter directive:', function() {

    var $compile;
    var $rootScope;

    beforeEach(function() {
        module('my.module');
        inject(function(\_$compile\_, \_$rootScope\_){
            $compile = \_$compile\_;
            $rootScope = \_$rootScope\_;
        });
    });

    it('should call the given method on an on-enter-event', function() {
        $rootScope.test = jasmine.createSpy('test');
        var element = $compile('')($rootScope);
        $rootScope.$digest();
        var e = $.Event("keydown");
        e.which = 13;

        element.triggerHandler(e);

        expect($rootScope.test).toHaveBeenCalled();
    });
});

## What does the test do?

The test does the following: First we load the module which contains our directive and inject AngularJS' `$rootScope` and `$compile` service. As a directive is attached to the HTML, we need to take some HTML and compile it with AngularJS in order to make the directive work like in a real life scenario.

Now we create our actual test case. First we create a spy function (`jasmine.createSpy('test');`) and attach it to our scope (which is simply the root scope). This function should be called by the directive if it works correctly. Now we create some HTML which uses the directive and compile it. The last thing we need to do is to create an on-enter-event and fire it up on the compiled element. This simulates the user pressing enter.

Last but not least we verify that the on-enter directive worked and has invoked our test function on the root scope.

## What the test doesn't do

The test only verifies the happy path. It tests if the function is called when enter is pressed, but it doesn't verify the opposite: Is the directive only listening on "enter" or will it also call our function if another key is pressed? However, implementing this test shouldn't be a big thing now. **Best regards,** Thomas

# More

[https://docs.angularjs.org/guide/directive](https://docs.angularjs.org/guide/directive)
