---
layout: post
title: "Implementing Interfaces and Abstract Classes on the fly"
date: "2012-05-18"
categories: 
  - "coding"
tags: 
  - "java"
  - "programming"
  - "reflection"
---

I'm currently working on a small LISP-like language for an university course of mine. Hence, the the language is implemented in Java, I wanted to make the Java library accessible withing my language. Though loading classes, invoking methods and reading fields is just one of two ways - _from_ Java _to_ my language. But what about the other direction? **How can I implement Java interfaces and abstract classes within my language to pass them to Java class as listeners, observers or handlers?**

#### Approach 1: java.lang.reflect.Proxy

The class [java.lang.reflect.Proxy](http://docs.oracle.com/javase/7/docs/api/java/lang/reflect/Proxy.html) is part of the Java Reflection API and enables you to create a proxy for every given interface. Its use is very straight forward:

public class Test {

     public static void main(String\[\] args) {

        ClassLoader cl = Test.class.getClassLoader();
        Class\[\] interfaces = new Class\[\]{ ActionListener.class };
	    
        Object object = Proxy.newProxyInstance(cl, interfaces, new InvocationHandler() {

            public Object invoke(Object proxy, Method method, Object\[\] args) throws Throwable {

                String name = method.getName();

                if(name.equals("toString")) {
                	
                    return "toString was called";
                }
                else if(name.equals("actionPerformed")) {
                	
                    System.out.println("actionPerformed was called");
                	
                    return null;
                }

                throw new RuntimeException("no method found");
            }
        });
		
	((ActionListener) object).actionPerformed(null);
    }
}

The principle of the Proxy class is relatively simple. The class will create a new object (or also a class if you want) that inherit from the Proxy class itself. This new object will implement all method of the specified interfaces. Each method of this interfaces will be redirected to the given invocation handler. In this handler, you can manage the various method calls individually (according to the method name and the given input parameters). The problem of this approach is that it's only working for interfaces, not for abstract classes since all created objects inheriting from the Proxy class itself. This is where [Javassist](http://www.jboss.org/javassist) comes into play.

#### Approach 2: javassist.util.proxy.ProxyFactory

The class [javassist.util.proxy.ProxyFactory](http://www.csg.is.titech.ac.jp/~chiba/javassist/html/javassist/util/proxy/ProxyFactory.html) is part of the [Javassist](http://www.jboss.org/javassist) library from [JBoss](http://www.jboss.org). The Javassist library provides powerful abilities for Java reflection, like creating new classes on the fly, compiling Java code during the program execution and many more - like implementing interfaces _and_ abstract classes. The solution from Javassist looks quite similar to the example above. It goes like this:

public class Test {

	public static void main(String\[\] args) throws Exception {

	    ProxyFactory factory = new ProxyFactory();
	
            factory.setInterfaces(new Class\[\] { ActionListener.class });

            factory.setHandler(new MethodHandler() {
			
		public Object invoke(Object arg0, Method method, Method arg2, Object\[\] arg3) throws Throwable {
			
                    String name = method.getName();

                    if(name.equals("toString")) {
                	
                        return "toString was called";
                    }
                    else if(name.equals("actionPerformed")) {
                	
                	System.out.println("actionPerformed was called");
                	
                	return null;
                    }

                    throw new RuntimeException("no method found");
		}
	    });
		
	    Class c = factory.createClass();

	    Object object = c.newInstance();
		 
	    ((ActionListener) object).actionPerformed(null);
	}
}

The main difference of this approach is that you can easily set an arbitrary super class:

factory.setSuperclass(MouseAdapter.class);

This could be any class as well as any abstract class. You handler will be called for every method-call on this class:

public Object invoke(Object arg0, Method method, Method arg2, Object\[\] arg3) throws Throwable {
			
    String name = method.getName();

    if(name.equals("mouseClicked")) {
                	
        System.out.println("mouseClicked was called");
                	
        return null;
    }

    throw new RuntimeException("no method found");
}

Javassist is published under either MPL, LGPL, or Apache License. So it's a real good alternative (or extension) to the standard reflection API of Java.

**Best regards**, Thomas Uhrig.
