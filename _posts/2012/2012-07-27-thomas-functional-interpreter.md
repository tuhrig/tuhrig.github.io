---
layout: post
title: "Thomas' Functional Interpreter"
date: "2012-07-27"
categories: 
  - "academic"
  - "coding"
  - "java"
  - "uml"
---

I just finished a small functional interpreter for an [university course](http://www.hdm-stuttgart.de/studienangebot/vorlesungsverzeichnis/vorlesung_detail?vorlid=5210910). 
To be precise, the interpreter itself is not functional, but it understands a functional, **LISP-like syntax**. 
The whole project is written in Java 7 and provides an interface to all Java classes on the class-path, too.

I also tried to create a small IDE in Swing. 
I built an editor with syntax-highlighting and code-completion (thanks to the [RSyntaxTextArea project](http://fifesoft.com/rsyntaxtextarea/)), an environment inspector and a small debugger to keep track of the stack. 
Take a look at it.

I called it **ThoFu** for _Thomas' Functional Interpreter_. 
You can get some brief impressions from the images below.

The whole project is open source, you can find it on [my GitHub account](https://github.com/tuhrig/functional-interpreter).
It contains the actual code, all libraries, screen-shots, unit tests and (of course) some documentation like UML diagrams.

Here is the executable JAR-file.
Just download it, double-click it and go-ahead:

- 📥 [ThoFu Interpreter.jar](https://github.com/tuhrig/functional-interpreter/raw/master/ThoFu%20Interpreter.jar)
- 📋 [README.md](https://github.com/tuhrig/functional-interpreter/blob/master/README.md)

## Screenshots

![](/images/2012/07/hello_world.png)
![](/images/2012/07/code_completion.png)
![](/images/2012/07/debugging.png)
![](/images/2012/07/creating_jframe.png)
![](/images/2012/07/history_view_of_creating_an_object.png)
![](/images/2012/07/inspector.png)
![](/images/2012/07/run_files.png)
![](/images/2012/07/working_with_different_files.png)
![](/images/2012/07/working_with_objects.png)

## Examples

And this is how it goes. 
First a simple "hello world" program:

```lisp
(print "hello world")
```

Now, a more sophisticated one. 
Here's a [quicksort-algorithm](https://www.geeksforgeeks.org/quick-sort/) for my interpreter. 
It contains some user-defined functions, multi-line statements and some comments.

```lisp
; Utils
(define mylength (lambda (L) (if (eq? L null) 0 (+ (mylength   (rest L)) 1))))
(define (append2 l1 l2) (if (eq? l1 null) l2 (cons (first l1) (append2 (rest l1) l2))))
(define (append3 a b c) (append2 a (append2 b c)))
(define (myfilter pred list) (if (eq? list null) null (if (pred (first list)) (cons (first list) (myfilter pred (rest list))) (myfilter pred (rest list)))))

; Quicksort
(define (quicksort list)
	(if (<= (mylength list) 1) 
		list 
	; else	
                (let ((pivot (first list))) 
                  (append3 
			(quicksort (myfilter (lambda (x) (< x pivot)) list))
			(myfilter  (lambda (x) (eq? x pivot)) list)
			(quicksort (myfilter  (lambda (x) (> x pivot)) list))))))
			
; Usage
(quicksort '(8 5 2 3 1))
```

And this is how the Java API works. 
The code will create a `JFrame` with a `JButton` and a `JLabel`. 
When you press the button, the listener will change the text of the label from "_click me_" to "_hello_". 
The syntax is inspired by the [Java Dot Notation](http://jscheme.sourceforge.net/jscheme/doc/javadot.html) by [Peter Norvig](http://norvig.com/). 
I used the [Apache Commons Lang](http://commons.apache.org/lang) library for the reflection-tricks to load class and so on.

```lisp
; Imports
(import "java.awt.event.\*")
(import "javax.swing.\*")
(import "java.lang.\*")
(import "java.awt.\*")

; Components
(define win (JFrame. "JavaExample1"))
(define button (JButton. "click me"))
(define label (JLabel. "nothing"))
(define layout (FlowLayout.))

; Variables
(define visible (Boolean. true))
(define size (Integer. 300))

; Listeners
(define (listener name)
	(define (actionPerformed e) (.setText label "hello"))
	(if (eq? name 'actionPerformed) actionPerformed error))

(define int (interface ActionListener.class listener))

; Construction 
(.setLayout win layout)
(.setSize win size size)
(.add win button)
(.add win label)
(.addActionListener button int)
(.setVisible win visible))
```

You can just copy and paste the code snippets into the editor or save them as common text files and open them. 
They are also in the repository. 
The JUnit tests contain many more examples as well.

For those who are interested in the topic, you can find my resources (libraries, tool and literature) in the 📋 [README.md](https://github.com/tuhrig/functional-interpreter/blob/master/README.md)).

**Best regards,** Thomas.
