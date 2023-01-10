---
layout: post
title: "Reusable AngularJS form components"
date: "2016-10-31"
categories: 
  - "coding"
  - "angularjs"
  - "javascript"
---

Whenever you build software, you want to create reusable and independent components.
In the world of user interfaces, those components are often called widgets. 
Here's an example how we can achieve this with [AngularJS](https://angularjs.org/).

## An example

Let's say we have an online shop where a customer can make an order. 
To do so, the customer must provide some data, in particular an invoice address and a delivery address:

```html
<html>
  ...
  <div ng-controller="OrderController as controller">
    <form>

      First Name: <input type="text" ng-model="controller.data.person.firstName">
      Last Name: <input type="text" ng-model="controller.data.person.lastName">

      <b>Invoice Address</b>
      Street: <input type="text" ng-model="controller.data.invoiceAddress.street">
      City: <input type="text" ng-model="controller.data.invoiceAddress.city">

      <b>Delivery Address</b>
      Street: <input type="text" ng-model="controller.data.deliveryAddress.street">
      City: <input type="text" ng-model="controller.data.deliveryAddress.city">

    </form>
  </div>
  ...
```

Both, invoice address and delivery address, look exactly the same. 
They are an example for UI elements which can be bundled in a reusable component.

## AngularJS UI components

With version 1.5, AngularJS introduced the concept of components. 
AngularJS' components are a syntactic short-cut for [directives](https://docs.angularjs.org/guide/directive). 
So if you use an AngularJS version prior to 1.5, you can achieve the same behaviour with directives - just with a little more code.

An AngularJS component for our address example could look like this:

```javascript
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
```

The `my.address.html` template would look like this:

```html
Street: <input type="text" ng-model="$ctrl.address.street">
City: <input type="text" ng-model="$ctrl.address.city">
```

If we have this two pieces of code, we can use our brand-new component:

```html
<html>
  ...
  <div ng-controller="OrderController as controller">
    <form>

      First Name: <input type="text" ng-model="controller.data.person.firstName">
      Last Name: <input type="text" ng-model="controller.data.person.lastName">

      <b>Invoice Address</b>
      <my-address address="controller.data.invoiceAddress"></my-address>

      <b>Delivery Address</b>
      <my-address address="controller.data.deliveryAddress"></my-address>

    </form>
  </div>
  ...
```

## Pitfalls

Although the example is very simple, there are already some pitfalls you need to be aware off.

- While we have named our component `myAddress` (camelCase) in our JavaScript code, we need to name it `my-address` in our HTML code. 
Otherwise, the component will not be recognized.
- We used the controller-as syntax to name our `OrderController` just `controller`. 
However, inside the template of our component, we must refer to the controller as `$ctrl` by default.
- We bind our data to a property which we called `address`. 
We can name this property totally as we like, but we need to make sure that we refer to this property from inside our template: `ng-model="$ctrl.address.city"`.
- We have used a bi-directional binding using the `=` operator. 
So if the model changes inside our component (because the user has entered some text in one of the input fields) it will affect the same object inside our parent controller. 
Otherwise, we wouldn't be able to access the data.

## Accessing the data

Now that we have successfully added our component to our page, we also want to use the data of the delivery and invoice address. 
Doing this in our parent controller (`OrderController`) is quite easy:

```javascript
var self = this;
console.log(self.data);
```

The result would look like this:

```javascript
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
```

Note that `invoiceAddress` and `deliveryAddress` is nested in an object called `data` because we set up the component like that:

```html
<my-address data="controller.data.invoiceAddress"></my-address>
```

## Form validation

Another pitfall which might be a little tricky is form validation. 
Let's start with an example again. 
First, we add form validation to our initial example where we don't use any components:

```html
<html>
  ...
  <div ng-controller="OrderController as controller">
    <form name="addressForm">

      First Name: <input type="text" 
                         required 
                         name="firstName"
                         ng-model="controller.data.person.firstName"
                         ng-class="addressForm.firstName.$invalid? 'has-error' : 'has-success'">


      Last Name: <input type="text" 
                        required 
                        name="lastName"
                        ng-model="controller.data.person.lastName"
                        ng-class="addressForm.lastName.$invalid? 'has-error' : 'has-success'">

      ...

    </form>
  </div>
  ...
```

This is an example of a very simple form validation with build-in AngularJS functionality. 
If one of the input fields (`firstName` or `lastName`) is invalid, a certain CSS class will be applied to mark the field as visually for the user. 
If we do the very same for our component, we will come to a problem: 
In order to apply the CSS class with `ng-class` we need a reference to the form:

```javascript
ng-class="addressForm.lastName.$invalid? 'has-error' : 'has-success'"
```

Since we want to make an independent component, we don't want the component to know something about the form in which it is included. 
This would break encapsulation and we couldn't reuse the component. 
But we cannot put the component in its own form, because HTML doesn't allow nested forms. 
So if we would package our component into its own little form, the browser would ignore and remove it:

![](/images/2016/10/html-form.png)

The solution is to use `ng-form` instead of HTML's `form`. 
It will enable AngularJS' form validation, but will not be touched by the browser. 
Our component will look like this:

```html
<div ng-form name="addressComponentForm">
Street: <input type="text" 
               required
               name="street"
               ng-model="$ctrl.address.street"
               ng-class="addressComponentForm.street.$invalid? 'has-error' : 'has-success'">
City: <input type="text" 
             required
             name="city"
             ng-model="$ctrl.address.city"
             ng-class="addressComponentForm.city.$invalid? 'has-error' : 'has-success'">
</div>
```

## Plunker

You can see the complete working example here: [https://plnkr.co/6UyqVn](https://plnkr.co/6UyqVn)

## More

- [https://docs.angularjs.org/guide/component](https://docs.angularjs.org/guide/component)
- [http://stackoverflow.com/questions/22098584/angularjs-input-validation-with-no-enclosing-form](http://stackoverflow.com/questions/22098584/angularjs-input-validation-with-no-enclosing-form)

**Best regards,** Thomas.
