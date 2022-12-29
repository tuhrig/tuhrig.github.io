---
layout: post
title: "Simple permission handling in AngularJS"
date: "2017-12-21"
categories: 
  - "coding"
  - "angularjs"
  - "security"
---

Despite a simple authentication, a lot of applications have a concept of authorisation. 
A user is not only logged-in, but has a set of roles which allow him to perform certain actions or see certain views. 
For example a user with the role `ADMIN` will probably be able to do more things as a user with the role `CUSTOMER`.

How can we support this in [AngularJS](https://angularjs.org)?

## Get the user

Before we can deal with any kind of user permissions, we first must have access to the login context of the user. 
We can do so, by implementing a backend REST-controller which returns the current user. 
This user object holds a list of _permissions_ (or _roles_, _privileges_ or what ever you call them).

```java
@RestController
@RequestMapping("/users")
public class UserController {
    @RequestMapping(path = "/current", method = RequestMethod.GET)
    public UserResource currentUser() {
        UserResource userResource = new UserResource();
        userResource.setUserName(...);
        userResource.setPermissions(...); // e.g. VIEW_ADMIN_PANEL, VIEW_PAYMENT_DATA, etc.
        return userResource;
    }
}
```

We can wrap this backend controller in an AngularJS service:

```javascript
(function () {
    angular.module('my.app').factory('UserService', UserServiceFactory);
    UserServiceFactory.$inject = ['$http', '$q'];
    function UserServiceFactory($http, $q) {
        var user;
        function getUser() {
            if(user) {
                var deferred = $q.defer();
                deferred.resolve(user);
                return deferred.promise;
            } else {
                return $http.get('users/current').then(function (response) {
                    user = response.data;
                    return user;
                });
            }
        }
        return {
            getUser: getUser
        };
    }
})();
```

Now we have access to the current user and access its roles.

## Check the permissions

With the user in our hands we can check its permissions. 
We can compare the permissions which the user actually has against a list of permissions which are needed to execute or view a certain thing.

```javascript
function currentUserHasAnyPermission(requiredPermissions) {
    return UserService.getUser().then(function (currentUser) {
        var currentUserPermissions = currentUser.permissions;
        return _.intersection(currentUserPermissions, requiredPermissions).length > 0;
    });
}

// returns a promise which resolves to 'true' or 'false'
currentUserHasAnyPermission(['VIEW_ADMIN_PANEL', 'VIEW_PAYMENT_DATA']);
```

## Protecting components

The last step is to actually protect components of our AngularJS application. 
There are a lot of different approaches to do so, for example by [routing configurations](https://github.com/Narzerus/angular-permission). 
However, I want to demonstrate a more simple solution which can not only be used for routes, but for any AngularJS component.

The idea is to implement a directive which is configured with a list of permissions. 
If the user has none of these permissions the directive will hide the element which it annotates. 
The element will only be visible if the user has at least one of the given permissions.

```javascript
(function(){
    angular.module('my.app').directive('guard', GuardDirective);
    GuardDirective.$inject = ['UserService'];
    function GuardDirective(UserService) {
        return {
            restrict: 'A',
            link: function (scope, element, attrs) {
                element.hide();
                // We cannot pass an array to the directive. As a workaround we
                // split the argument string and convert it to an array.
                var permissions = attrs.guard.split(",");
                for (var i = 0; i < permissions.length; i++) {
                    permissions[i] = permissions[i].trim();
                }
                currentUserHasAnyPermission(permissions).then(function (isAllowed) {
                    if(isAllowed) {
                        element.show();
                    }
                });
                function currentUserHasAnyPermission(requiredPermissions) {
                    // See above...
                }
            }
        }
    }
})();
```

By putting this directive to an arbitrary HTML element, the element will only be visible if the user has the given permissions:

```html
<ul class="nav nav-tabs">
    <li>
        <a ui-sref="...">Home</a>
    </li>
    <li guard="VIEW_ADMIN_PANEL">
        <a ui-sref="...">Admin Panel</a>
    </li>
    <li guard="VIEW_ADMIN_PANEL, VIEW_PAYMENT_DATA">
        <a ui-sref="...">Payment Panel</a>
    </li>
</ul>
```

In the example above, the _Home_ view can be seen by everyone. 
It's not protected. 
The _Admin Panel_ on the other hand can only be seen by a user with the permission `VIEW_ADMIN_PANEL`. 
Last but not least, the _Payment Panel_ is available for any user which either has the permission `VIEW_ADMIN_PANEL` or `VIEW_PAYMENT_DATA`.

## Pros and Cons

The solution is very simple and requires no additional framework or library. 
The protection can be applied to any HTML element, which makes it very flexibel. 
A downside is that the permission handling might be scattered around the frontend. 
It will not be in a single place as it would be with an approach based on routing.

## Is it secure?

Of course not! 
Any security pattern applied to the UI will be unsecure. 
The UI runs on the client side and the user is able to modify all of its code. 
No matter which pattern we use, the user can easily get around it. 
In the end, any REST call can be sent to the backend directly without even using the UI.

This being said, it's very important to protect and secure the backend in the first place. 
The backend must apply and enforce the permissions. 
The frontend is just an unsecure client which nobody can trust. 
It's only concern is to offer a good experience to the user.

**Best regards,** Thomas.
