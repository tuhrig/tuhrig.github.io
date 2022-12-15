---
layout: post
title: "Java 8 in Eclipse (Juno)"
date: "2012-10-10"
categories: 
  - "coding"
tags: 
  - "eclipse"
  - "java"
  - "java-8"
  - "programming"
  - "testing"
---

* * *

**Note:** Here is an up-to-date tutorial for the new Eclipse versions Kepler and Luna: [http://tuhrig.de/java-8-in-eclipse-kepler-and-luna](http://tuhrig.de/java-8-in-eclipse-kepler-and-luna)

* * *

Since last July the _new_ Java 7 is generally available. However, even the _newer_ Java 8 is already available as an **Early Access Preview**. It can be download as part of the **OpenJDK** including **lambda-support**. To use it with Eclipse, some additional steps are required. Here's how it goes:

1. Download the JDK from [http://jdk8.java.net/lambda](http://jdk8.java.net/lambda "Java 8 with lambda-support") and install it.
2. Create a new Java project in Eclipse.
3. Change some project settings in Eclipse in order to use the new javac compiler of Java 8:
    1. Click right on your project and select _Properties_.
    2. Select _Builders_ on the left side and uncheck the default _Java Builder_.
    3. Click on _New_ and select _Program_ as the type of the new builder.
    4. Open _javac_ from the JDK 8 on your disk (it's in the folder _bin_).
    5. Configure the new builder by providing some arguments:
        
        \-source 8
        -d ${workspace\_loc:/Java8}/bin
        ${workspace\_loc:/Java8}/src/de/tuhrig/java8/\*.java
        
    6. Select the tab _Build Options_ and check the _During auto builds_ box, to let the builder run automatically.
4. Done! Now you can write and execute Java 8 code!

\[gallery ids="1966,1967,1968,1969,1970,1971"\]

The main feature of Java 8 is the ability to write lambdas and use a functional-like programming style. This can be very useful, especially in GUI-programming. It can reduce the amount of code, because anonymous classes that only implements a listener-interface are avoided. Here's a very simple example:

public class Test {

    public static void main(String\[\] args) {

        JFrame frame = new JFrame();
        frame.setLayout(new FlowLayout());
        frame.setVisible(true);
		
        JButton button1 = new JButton("Click me!");
        JButton button2 = new JButton("Click me too!");
		
        frame.getContentPane().add(button1);
        frame.getContentPane().add(button2);
		
        button1.addActionListener(Test::doSomething);
        button2.addActionListener(e -> { System.out.println("Bye!"); });
    }
	
    public static void doSomething(ActionEvent e) {
		
        System.out.println("Hi!");
    }
}

The code creates a common JFrame with two buttons. To each button an action-listener is added. But instead of implementing an anonymous class, a method reference is passed in the first case and a lambda in the second case. This reduces the amount of code compared with the old solution that looked like this:

button.addActionListener(new ActionListener() {
			
    @Override
    public void actionPerformed(ActionEvent e) {

        System.out.println("...");
    }
});

However, the Java compiler will create exactly such a thing of the lambda expression or function reference. Therefore, the passed method must fulfill the interface described by ActionListener. Also, we can only use lambdas instead of interfaces that only describe one single method. Such interfaces are called _functional interfaces_.

Since Eclipse Juno is made for Java 7 it will not recognize the new lambda syntax. Hence, the editor will mark parts of the code as red errors. But it's still possible to compile and to execute the code using the new javac compiler form Java 8.

\[gallery columns="2" ids="1973,1974"\]

**Best regards,** Thomas Uhrig
