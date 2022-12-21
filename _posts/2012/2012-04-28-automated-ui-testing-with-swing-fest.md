---
layout: post
title: "Automated UI testing with Swing & FEST"
date: "2012-04-28"
categories: 
  - "academic"
  - "coding"
  - "design-pattern"
  - "java"
---

Testing an application is hard, but testing an user interface (UI) is even harder. [FEST](http://fest.easytesting.org/ "FEST") addresses this issue for **Java Swing** applications. It enables you to write easy and readable functional tests for your UI. Working together with [JUnit](http://www.junit.org/ "JUnit"), it lets your functional UI tests feel like common unit tests within your test suite.

FEST is a **context-sensitive** testing-framework, which means that the position of your UI components, their size or color isn't important for the test (of course you can tests this criteria if you want). FEST identifies all UI components by an unique ID - their name. Therefore, your UI layout can change, but the tests will still work.

Component component = new JButton();
component.setName("aName");
String name = component.getName();

By the way: Because _all_ components in your UI _must_ have a _unique name_, it's a good idea to use a [Factory](http://en.wikipedia.org/wiki/Factory_method_pattern "Factory Pattern") to create your components and to make sure that they all have an unique name.

To start with FEST is really simple:

1. [Download](http://code.google.com/p/fest/downloads/list "FEST Donwload") FEST from their [Google Code repository](http://code.google.com/p/fest/ "FEST on Google Code") and put fest-swing-x.x.jar as well as all JARs in the lib folder of the ZIP-file to your classpath.
2. Make a Swing UI, perhaps something like this:
    
     
    public class Demo extends JFrame {
    
    	private JButton button = new JButton("Click Me!");
    	private JLabel label = new JLabel("Button wasn't clicked!");
    
    	public Demo() {
    
    		this.getContentPane().setLayout(new FlowLayout());
    		this.getContentPane().add(button);
    		this.getContentPane().add(label);
    
    		button.setName("myButton");
    		label.setName("myLabel");
    
    		button.addActionListener(new ActionListener() {	
    
    			public void actionPerformed(ActionEvent e) {
    	
    				label.setText("Button was clicked!");
    			}
    		});
    		
    		this.pack();
    		this.setVisible(true);
    	}
    	
    	public static void main(String\[\] args) {
    
    		new Demo();
    	}
    }
    
3. Now, go ahead and write a (JUnit) test:
    
    public class DemoTest {
    
    	private FrameFixture demo;
    	
    	@Before
    	public void setUp() {
    
    		demo = new FrameFixture(new Demo());
    	}
    	
    	@After
    	public void tearDown() {
    
    		demo.cleanUp();
    	}
    	
    	@Test
    	public void test() {
    		
    		demo.label("myLabel").requireText("Button wasn't clicked!");
    		demo.button("myButton").click();
    		demo.label("myLabel").requireText("Button was clicked!");
    	}
    }
    
4. Let your test run and see how FEST is interacting live with your UI.

FEST uses the [java.awt.Robot](http://docs.oracle.com/javase/7/docs/api/java/awt/Robot.html "JavaDoc for the Robot class") class to interact with your UI. Robot is part of the JDK since version 1.3 and provides a low-level API to simulate user interaction for AWT or Swing application. FEST encapsulate this class into an abstract and easy to use API.

The difference between common JUnit tests and functional tests using FEST is the level of test-execution. JUnit tests are working on isolated classes and methods whereas functional tests (with FEST) are working on the real UI shown to the user. This _real_ UI can be composed of many single objects that should be tested altogether. Also, functional test doesn't work on a method-level - they are working on the same level as the human user simulating "clicks", "selects" and "keys".

**More:**

- [Test-driven GUI development with FEST](http://www.javaworld.com/javaworld/jw-07-2007/jw-07-fest.html "Test-driven GUI development with FEST") @ JavaWorld.com
- [Automatisierung des Tests von Java-Swing-GUIs](http://www.sigs.de/publications/js/2004/06/glatschke_JS_06_04.pdf "Automatisierung des Tests von Java-Swing-GUIs") @ SIGS.de

**Best regards**, Thomas Uhrig.
