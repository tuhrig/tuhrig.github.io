---
layout: post
title: "When modularity comes down to OSGi"
date: "2015-05-03"
categories: 
  - "coding"
tags: 
  - "java"
  - "modularity"
  - "osgi"
  - "techtrends"
---

Last week I attended a tech talk by [Christian Schlichtherle](http://schlichtherle.de), organized by [Euro Staff](http://www.eurostaffgroup.de) in Berlin. The talk was about modularity and its different forms in software development. Christian talked about packages, libraries, APIs, dependency management and - as always when it comes down to modularity - about OSGi.

Every time somebody is talking about OSGi I get this tiny pain in my stomach. OSGi is a really great idea and a lot of people are working with it for many years right now. That's cool! But I still have some questions about this...

# Who wants to install code during run time on a server?

Let's start with my biggest point of criticism. **OSGi has two main points:**

- OSGi (maybe) solves the class path hell (we will come to this later)
- OSGi introduces modularity during run time for Java software

But really - who builds software with a highly static language such as Java but then installs code during run time on his production environment!? Maybe I just don't know the use-case, but every company I saw so far is looking for a stable server architecture. They want to deploy something in their test environment, then go to staging and finally do a green-blue-deployment. They don't want to throw some new software in and let itself install it. And if they would do so, I would wonder how they deal with open user sessions, with buggy updates which breaks the system, with security and so on. In my opinion there are only very few cases where you really need and want modularity on the server.

# OSGi is solving the class path hell!

It's often said that OSGi is solving the class path hell. And when somebody is saying this, he comes up with the [diamond problem](http://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem):

Some package A needs a package B and C. B and C need a package D but in two different version. And you are screwed!

          \[Package A\]
              |   
      \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 
      |               |
      |               | 
 \[Package B\]    \[Pachage C\]
      |               |
v 1.5 |\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_| v 2.0
              |
          \[Package D\]   
          
        v 1.5 or v 2.0?  

OSGi solves this problem, because every package will get its own class loader and every Java class is not only identified by its name but also by it class loaded. This makes it possible to load the same class in different version for package B and C. Problem solved!

But what happens when package B and C are returning objects from package D? Let's make some stupid example: Package D contains a model: `Person.class`, `Address.class`. Package B and C are different factory strategies for this model. Package B reads data from a web service and package C from a local file. And package A is using both of them:

public class SomeClassFromPackageA {
    public User createUser() {
        User user = serviceFromPackageB.getUser();
        serviceFromPackageA.addAddress(user);
        return user;
    }
}

The problem with this code is that it looks perfectly fine and will compile, too. **But it will fail on run time with a class cast exception!** Why? Because `User.class` from package B was loaded with a different class loader as `User.class` from package C. So for Java, those are two different classes!

To solve this problem in OSGi you would write a service with an interface in package B and C, describe it in XML and wire it in package A. You would expose those services explicitly and create bunch of XML. Then you would package everything with an OSGi compatible manifest, assign version number to each bundle, re-pack third party libraries to OSGi bundles too, define a start-up order for the bundles, load an OSGi run time environment and then - _a couple of hours later_ - you really solved the diamond problem.

# IMHO

I think solving the class path hell in OSGi means to introduce a class loader and bundle hell instead. In pure Java it's simple: if something is on the class path, you can use it. In OSGi, this is more complicated and if you miss to strictly use OSGi services to decouple you software you will end up with a highly coupled bunch of bundles which might throw class cast exception because you still cannot user version 1.5 and version 2 at the same time.

**Best regards,** Thomas
