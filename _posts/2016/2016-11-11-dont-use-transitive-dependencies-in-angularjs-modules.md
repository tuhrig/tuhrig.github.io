---
layout: post
title: "Don't use transitive dependencies in AngularJS modules"
date: "2016-11-11"
categories: 
  - "coding"
  - "javascript"
  - "angularjs"
---

Whenever you write an [AngularJS](https://angularjs.org/) application, you will use modules. 
Modules divide your application into small logical units. 
Some units might depend on others, while some might be totally independent.

```javascript
angular.module('UserModule',[]); // no dependency!
angular.module('UtilModule',[]); // no dependency!
angular.module('MainModule',[ // 2 dependencies!
    'UserModule',
    'UtilModule'
]);
```

In the example above, you see three modules. 
A module called `MainModule` depending on two other modules, called `UserModule` and `UtilModule`. 
Note that this is just a very simple example. 
In the real world, things become much more complicated.

Let's assume the following dependency: 
`UserModule` depends on some service from `UtilModule`. 
However, we forgot to add this dependency to the module declaration. 
But because we always run our `MainModule` for our application, everything will work perfectly fine.
`MainModule` will load the dependency which the other module will need. 
So we won't see this hidden transitive dependency easily.

However, we have problem now: 
we don't know anymore which dependencies our module has. 
Even worse, our module depends on another module, because it provides its dependencies. 
We end up with a cyclic dependency as `MainModule` depends on `UserModule` and vice versa. 
One cannot run without the other.

## But what's the problem?

It becomes a practical problem when it comes down to testing. 
Whenever you do unit testing, you want to test things isolated:

```javascript
beforeEach(function() {
    module('UserModule');
});
```

You want to test a small piece with as less dependencies as possible. 
But what happens if you didn't specify your dependencies right?

```javascript
beforeEach(function() {
    module('MainModule');
    module('UserModule');
});
```

You also need to load modules which you don't actually need. 
This could easily break your test if something in the other module changes.

Keep your modules clean!

**Best regards,** Thomas.
