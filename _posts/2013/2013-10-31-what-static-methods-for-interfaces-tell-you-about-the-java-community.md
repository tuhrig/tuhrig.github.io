---
layout: post
title: "What static methods for interfaces tell you about the Java community"
date: "2013-10-31"
categories: 
  - "coding"
tags: 
  - "community"
  - "design-pattern"
  - "java"
  - "java-8"
---

Recently I was putting together some code for an OSGi plugin at work. I created an interface to add listeners to an extension point. The code looked something like this:

public interface AfterActionListener {
      public ResultObject doSomething(MyObject myObject);
}

The idea was simple: At some points in my application I would call all registered listeners, pass them an object and let them do whatever they are supposed to do. The only problem was, that the code to call these listeners is pretty cumbersome:

public static Map< String, Object > invokeAll(MyObject myObject) {

      Map results = new HashMap<>();

      String extPointId = Activator.getDefault()
                                   .getBundle()
                                   .getSymbolicName() 
                                   + ".myExtensionId";

      IConfigurationElement\[\] elements = 
                  RegistryFactory.getRegistry()
                      .getConfigurationElementsFor(extPointId);

      for (IConfigurationElement element : elements){

          Object object = element.createExecutableExtension("class");

          if (object != null && object instanceof AfterActionListener){

              AfterActionListener listener = (AfterActionListener) object;
              String listenerName = listener.getClass().getName();
              ResultObject result = listener.doSomething(myObject);
              results.put(listenerName, result);
          }
      }
      return results;
} 

FML! In reality it looks even more cumbersome with some exception-handling and logging.

So the first thing I did was a utility class to put that code to and never see it again. But utility classes are not a very good practice at all. So I thought about a suitable place for my code. Some kind of a factory? Or some invoker object? But isn't that just another name for a utility class? Or... why not put it in the interface itself? So the method to deal with the interface would stay in the interface itself.

A minute and a compile time error later it came back to my mind: interface can't have static methods in Java! (I will come back to this later...)

# But why can't interfaces have static methods?

The question seems legit to me. Classes as well as abstract classes can have static methods and interfaces can have static classes and static fields. But no static methods. Why?

# Ask Google!

I did what I'm always doing when I have a question, I asked Google. Five minutes later I had a bunch of different answers:

> It's not possible!

Everything is possible. Especially in Computer Science.

> It doesn't make sense!

I can give you 10 situations in a minute where it makes sense. Let's start with the one above.

> Your desing is wrong!

Is it? Would a new class with just a single method make it better?

> You know nothing about OO!

So if I put a static method in an abstract class it's called a pattern (the Factory Pattern of the GoF) and if I want to do something similar in an interface, I have no clue what I'm doing? Languages like Ruby which are OO from the ground up (not like Java) have similar concepts.

# The truth?

As always in software development there's no certainty. There are a thousand ways to implement the case a both, every with its own pros and cons. One is beautiful OO, one reads nice, another one is easy to test and one is just as sophisticated as it could be. But the best explanation I found is this:

> It's just a design decision of the Java makers.

It is! Like everything that is in the language or not. And as many things that were discussed over the years, it will finally come with the next Java release. **Java 8 will have static methods for interfaces**. It will also have default implementations for interfaces and Lambda-expressions. OO? Nope, just good to write code.

# The community

I looked in a dozen different forums and Q&A sites for a proper explanation. But I didn't find much good answers. It is just a decision of some guys made a couple of years ago. And right now, it seems that they are doing the very reverse - put static methods for interface in Java 8.

But what really shock me is the way it is discussed in the community. A lot of people are almost religious about it. They don't have a clue what it's about, so it must be huge design mistake, not OO or even not possible.

I think strong opinions are never. Not in politics, not in art and especially not in software development. Maybe that's why Java moves as fast as it does?

# The hack

Btw, there is a very easy hack to add static methods to an interface in Java 7. Interfaces can have static classes which can have static methods. It's not the nicest thing ever, but at least it lets you put your "utility class" together with the interface.

public interface AfterActionListener {
      public ResultObject doSomething(MyObject myObject);
      static class Invoker {
            public static Map< String, Object > invokeAll(MyObject myObject) {
                  ...
            }
      }
}

**Best regards**, Thomas
