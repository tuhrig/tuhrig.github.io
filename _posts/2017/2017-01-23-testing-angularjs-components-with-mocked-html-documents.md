---
layout: post
title: "AngularJS tests with mocked HTML documents"
date: "2017-01-23"
categories: 
  - "coding"
tags: 
  - "angularjs"
  - "javascript"
  - "testing"
coverImage: "car-1081836_1280.jpg"
---

Most of the time, testing [AngularJS](https://angularjs.org/) applications means testing JavaScript. We test services and we test controllers. But what about directives? What about components? How do we test compiled and injected AngularJS code?

# Plunker

But before we start, here's the running Plunker with the complete example: [https://plnkr.co/edit/PKn4tpUDFWyeqJ2Tb7Ba](https://plnkr.co/edit/PKn4tpUDFWyeqJ2Tb7Ba)

# Testing directives/components

Around a year ago I wrote a post on [testing AngularJS directives with Jasmine](http://tuhrig.de/testing-angularjs-directives-with-jasmine). Let's take a look at this article with a slightly modified example. While I used a directive in my last post back in 2016, we use one of AngularJS' components, now. It's 2017!

Here's the component:

app.component('text', {
    template: "" +
                  "Input: Valid: {{textForm.$valid}}" + 
              ""
});

The component shows a simple text field which is required. The `textForm` becomes valid if some text is filled into this required text field.

## The test

Testing this simple component is very straight forward. We just need to compile the HTML and run our tests against it. As AngularJS components use an isolated scope, we need to call `element.isolateScope()` to get the scope (including our form) of the component.

describe('Text directive', function() {

    var $compile;
    var $rootScope;
 
    beforeEach(function() {
        module('plunker');
        inject(function(\_$compile\_, \_$rootScope\_){
            $compile = \_$compile\_;
            $rootScope = \_$rootScope\_;
        });
    });
 
    it('should be invalid as text field is empty but required', function() {
        var element = $compile('')($rootScope);
        $rootScope.$digest();
        expect(element.isolateScope().textForm.$valid).toBe(false);
    });
    
        it('should become valid if text is entered in required text field', function() {
        var element = $compile('')($rootScope);
        $rootScope.$digest();
        $rootScope.$$childTail.textForm.name.$setViewValue("some text");
        expect(element.isolateScope().textForm.$valid).toBe(true);
    });
});

# So what about the document?

Although we compiled some HTML code in the example above, we didn't use any test document! There was no need. So let's extend our example a little bit and introduce some necessity to use a test document.

**Image the following:** We have some use case where we need to generate code. If an user has not entered his phone number yet, we display an input form. If the user has already entered his phone number, we display some text ("_Thank you for entering your phone number!_"). While we could easily use some `ng-if` or `ng-show` construction, we decided to do it with code generation.

This use case could look like this:

app.component('generator', {
    template: "

",
    controller: function($compile, $scope, $document) {
        var componentContainer = $document.find('#component-container');
        var template = "";
        var childScope = $scope.$new();
        var result = componentContainer.append(template);
        $compile(result)(childScope);
    }
});

We define a component called `generator`. This component has a very simple template, containing a placeholder `div` where we want to inject some other code. The main point is, that we are using the `$document` service. This service operates on the current document and is our way to create a mock document. But first, let's see what happens if we don't use a mock document.

describe('Generator directive', function() {

    var $compile;
    var $rootScope;
 
    beforeEach(function() {
        module('plunker');
        inject(function(\_$compile\_, \_$rootScope\_){
            $compile = \_$compile\_;
            $rootScope = \_$rootScope\_;
        });
    });
 
    it('should be invalid as generated text form is empty but required', function() {
        var element = $compile('')($rootScope);
        $rootScope.$digest();

        // THIS WILL NOT WORK! Although we compiled the 'generator' directive just like the 'text'
        // directive above, we forgot our test document. The 'generator' directive tries to append
        // its result to the document. However, the document is still empty, because have compiled 
        // our directive but we missed to append it to the document!
        expect(element.isolateScope().textForm.$valid).toBe(false);
    });
});

Because the `generator` directive appends code to the document (inside `#component-container`) we need to add the compiled `generator` directive to the document, too!

describe('Generator directive', function() {

    var mainForm = "

";

    var $compile;
    var $rootScope;
    var $document;
 
    beforeEach(function() {
        module('plunker');
        inject(function(\_$compile\_, \_$rootScope\_, \_$document\_){
            $compile = \_$compile\_;
            $rootScope = \_$rootScope\_;
            $document = \_$document\_;
        });
        
        $document.find('body').append(mainForm);
        $compile($document.find("body"))($rootScope);
    });
    
    afterEach(function() {
        $document.find('form').html(''); // clean the mocked document!
    });

    it('should be invalid as generated text form is empty but required', function() {
      
        var scope = $rootScope.$new();
        var element = angular.element('');
        $document.find("#container").append(element); // append it to the document!
        $compile(element)(scope);
        scope.$apply();
 
        expect($rootScope.mainForm.textForm.$valid).toBe(false);
    });
    
    it('should be invalid as generated text form is empty but required', function() {
      
        var scope = $rootScope.$new();
        var element = angular.element('');
        $document.find("#container").append(element); // append it to the document!
        $compile(element)(scope);
        scope.$apply();
 
        $rootScope.mainForm.textForm.name.$setViewValue("some text");
 
        expect($rootScope.mainForm.textForm.$valid).toBe(true);
    });
});

The important points are:

- We compile the `generator` directive **and** append it to the document.
- Any code generated by the `generator` directive will be placed inside the document.
- We need to clean the document after every test!

And by the way, here's the Plunker again: [https://plnkr.co/edit/PKn4tpUDFWyeqJ2Tb7Ba](https://plnkr.co/edit/PKn4tpUDFWyeqJ2Tb7Ba)

**Best regards,** Thomas
