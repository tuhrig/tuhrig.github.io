---
layout: post
title: "Tools to understand Java code"
date: "2012-05-01"
categories: 
  - "coding"
  - "java"
  - "uml"
---

I have recently read a [question on Stackoverflow](http://stackoverflow.com/questions/10387371/how-to-read-java-code-and-is-there-is-any-tool-to-make-it-more-understandable "Question @ Stackoverflow") whether there are any tools to help understanding Java code. Somebody asked it, because he started to use a pen and a sheet of paper to get the clue of some code given to him. Not uncommon.

But the problem was, that nobody tried to answer the question, instead giving him tips like "learn Java" or "read tutorials". I think they have misunderstood the question - because there are definitely tools for that:

- A good **IDE** just like [Eclipse](http://www.eclipse.org/ "Eclipse") is one basic tool to understand code. It helps you in various ways:
    
    - It shows the structure of folders and files of the project. You can easily get an overview of everything. Just [import](http://help.eclipse.org/indigo/index.jsp?topic=%2Forg.eclipse.platform.doc.user%2Ftasks%2Ftasks-53.xhtml) the project and have a look what you got.
    
    - It gives you the ability of syntax-highlighting (trivial, I know), but also the ability to [format](http://help.eclipse.org/indigo/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-java-editor-formatter.htm) code like your are used to in a second. That helps reading it, even (or _especially_?) when you have some experience in the language.
    
    - It shows you the [JavaDoc](http://help.eclipse.org/indigo/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-dialog-javadoc-location.htm) comments right away at every position in your code. So you will always have the documentation with you when needed.
    
    - It provides tools such as a [debugger](http://help.eclipse.org/indigo/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Fconcepts%2Fcdebugger.htm), a [call-hierarchy](http://help.eclipse.org/indigo/index.jsp?topic=%2Forg.eclipse.cdt.doc.user%2Freference%2Fcdt_u_call_hierarchy_view.htm) view and many [more](http://help.eclipse.org/indigo/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Fconcepts%2Fconcept-java-views.htm). All this tools will help you to get an understanding of how the code works.
    
    And it can to much more for you. So it's really worth to get familiar with your IDE.

- **UML** is a widely used standard for modeling and visualizing code. The purpose of UML is simple and exactly what was asked: it should help you to understand code. The strength of UML is the abstraction. You can easily visualize a big architecture without mention any detail at all. On the other hand, you can add any detail you want if it is important for the understanding of the system.
    
    The basics of UML are not hard to learn, a quick look at [Wikipedia](http://en.wikipedia.org/wiki/Unified_Modeling_Language "UML @ Wikipedia") should be enough to draw a class diagram. But UML is also enough standardized, that it could not only be read by humans, but also by machines - so you can generate it (so called _round-tripping_)!
    
    Tools like [ObjectAid](http://www.objectaid.com "Object Aid") helps you to generate class diagrams as easy as possible. Just install it in your IDE, create a new class diagram and drag all Java classes in it.

- **Tests**, especially unit tests (e.g. [JUnit](http://www.junit.org/ "JUnit.org") tests) could be also very useful to understand the code. A good test is something like a additional documentation or a contract on code-level. It exactly defines how a method can be used (e.g. input parameters) and what it should make (that's what the test is testing ;)). So, if you don't understand what a method should do and you have tests, take a look at them. **And what if you don't have any tests?** Write your own ones! This is a good approach to explore an API in a structured and repeatable way. Whenever you are not sure what the API does, try to create a test about your assumption.

\[gallery ids="2008,2009,2010,2011,2012,2013,2014,2015,2016,2017"\]

So, what to do if there is a bunch of new code?

1. Import the project to your IDE.
2. Get an overview of folders, files, packages and so on.
3. Explore the code: What's in the JavaDoc? What types are used? What about the call hierarchy?
4. Run the unit tests and try to understand what the methods are doing.
5. Try to run the debugger for a test or two.
6. Draw or generate a UMl diagram.

I have uploaded some screenshot in the slideshow above for the open-source [RSyntaxTextArea](http://fifesoft.com/rsyntaxtextarea/ "RSyntaxTextArea") library from [Fifesoft](http://fifesoft.com "Fifesoft"). But you will also find some good tutorials in the internet for every point mentioned before. Try to get familiar with at least some of the tools.

**Best regards**, Thomas Uhrig
