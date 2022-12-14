---
layout: post
title: "Reusable AngularJS form components"
date: "2016-10-31"
categories: 
  - "coding"
tags: 
  - "angularjs"
  - "components"
  - "javascript"
  - "validation"
coverImage: "recycled-1558544_1280.jpg"
---

Whenever you build software, you want to create reusable and independent components. In the world of user interfaces, those components are often called widgets. Here's an example how we can achieve this with AngularJS.

# An example

Let's say we have an online shop where a customer can make an order. To do so, the customer must provide some data, in particular an invoice address and a delivery address:

  ...
  

    

      First Name: 
      Last Name: 

      **Invoice Address**
      Street: 
      City: 

      **Delivery Address**
      Street: 
      City: 

    

  

  ...

Both, invoice address and delivery address, look exactly the same. They are an example for UI elements which can be bundled in an reusable component.

# An AngularJS UI component

With version 1.5 AngularJS introduced the concept of a component. AngularJS' components are a syntactic short-cut for common directives. So if you use an AngularJS version prior to 1.5, you can achieve the very same behaviour with directives - just with a little bit more code.

An AngularJS component for our address example could look like this:

angular.module('myApp.myModule').component('myAddress', {
    templateUrl: 'app/components/my.address.html',
    controller: function () {
        var self = this;
        console.log(self.address);
    },
    bindings: {
        address: '='
    }
});

The `my.address.html` template would look like this:

Street: 
City: 

If we got this two pieces of code, we can use our brand new component:

  ...
  

    

      First Name: 
      Last Name: 

      **Invoice Address**
      

      **Delivery Address**
      

    

  

  ...

# Pitfalls

Although our example is still very simple, there are already some pitfalls you need to be aware off.

- While we have named our component `myAddress` (camelCase) in our JavaScript code, we need to name it `my-address` in our HTML code. Otherwise the component will not be recognized.
- We used the controller-as syntax to name our `OrderController` just `controller`. However, inside the template of our component, we must refer to the controller as `$ctrl` by default.
- We bind our data to a property which we called `address`. We can name this property totally as we like, but we need to make sure that we refer to this property from inside of our template: `ng-model="$ctrl.address.city"`
- We have used a bi-directional binding using the `=` operator. So if the model changes inside our component (because the user has entered some text in one of the input fields) it will affect the same object inside our parent controller. Otherwise we wouldn't be able to access the data.

# Accessing the data

Now that we have successfully added our component to our page, we also want to use the data of the delivery and invoice address. Doing this in our parent controller (`OrderController`) is quite easy:

var self = this;
console.log(self.data);

The result would look like this:

self.data = {
  invoiceAddress: {
    street: "Mainstreet 1",
    city: "Berlin"
  },
  deliveryAddress: {
    street: "Walk of Fame 42",
    city: "Stuttgart"
  }
}

Note that `invoiceAddress` and `deliveryAddress` is nested in an object called `data` because we set-up the component like that:

# Form validation

Another pitfall which might be a little bit tricky is form validation. Let's start with an example again. First, we add form validation to our initial example where we don't use any components:

  ...
  

    

      First Name: 

      Last Name: 

      ...

    

  

  ...

This is an example of a very simple form validation build with AngularJS build-in functionality. If the one of the input fields (`firstName` or `lastName`) is invalid, a certain CSS class will be applied to mark the field as visually for the user. If we do the very same for our component, we will come to a problem: In order to apply the CSS class with `ng-class` we need a reference to the form:

ng-class="addressForm.lastName.$invalid? 'has-error' : 'has-success'"

Since we want to make an independent component, we don't want the component to know something about the form in which it is included. This would break encapsulation and we couldn't reuse the component wherever we want. But we cannot put the component in its own form, because HTML doesn't allow to nest form. So if we would package our component into its own little form, the browser would ignore and remove it:

[![bildschirmfoto-2016-10-31-um-09-15-07](images/Bildschirmfoto-2016-10-31-um-09.15.07.png)](http://tuhrig.de/wp-content/uploads/2016/10/Bildschirmfoto-2016-10-31-um-09.15.07.png)

The solution is to use `ng-form` instead of HTML's `form`. It will enable AngularJS' form validation, but will not be touched by the browser. Our component will look like this:

Street: 
City: 

# Plunker

You can see the complete working example here:

[https://plnkr.co/6UyqVn](https://plnkr.co/6UyqVn)

# More

- [https://docs.angularjs.org/guide/component](https://docs.angularjs.org/guide/component)
- [http://stackoverflow.com/questions/22098584/angularjs-input-validation-with-no-enclosing-form](http://stackoverflow.com/questions/22098584/angularjs-input-validation-with-no-enclosing-form)

**Best regards,** Thomas
