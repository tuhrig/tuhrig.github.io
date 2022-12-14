---
layout: post
title: "Generating PDFs with Java, Flying Saucer and Thymeleaf (Part 1)"
date: "2017-06-13"
categories: 
  - "coding"
  - "html"
  - "java"
---

Generating PDFs can be a tricky job. 
If you ever worked with templating engines like [Jasper](https://www.jaspersoft.com) you probably know what I mean. 
On the other side, HTML makes it extremely simple to describe documents. 
So why not use HTML to generate PDFs?

The following post shows a simple approach to generate PDFs with [Thymeleaf](http://www.thymeleaf.org) templates and [Flying Saucer](https://github.com/flyingsaucerproject/flyingsaucer) in plain Java.

> Example on GitHub: [https://github.com/tuhrig/Flying_Saucer_PDF_Generation](https://github.com/tuhrig/Flying_Saucer_PDF_Generation)

> [Part 2](http://tuhrig.de/generating-pdfs-with-java-flying-saucer-and-thymeleaf-part-2/) of this post for a deeper dive into styling with CSS and images.

## PDF rendering pipeline

![](/images/2017/06/rendering.png)

Our PDF rendering pipeline consists of two basic steps:

1. Using Thymeleaf, we populate XHTML templates with data in order to receive a plain XHTML document.
2. We save this XHTML document as a PDF using Flying Saucer.

Note that both steps are independent. 
You could easily use any other templating engine (just as [Apache FreeMarker](http://freemarker.org)) or even plain XHTML. 
However, I prefer Thymeleaf as it is a mature templating engine especially if you work in a [Spring](https://spring.io/) environment.

## Project Setup

To start rendering, we need three different dependencies which we can find in [Maven](https://mvnrepository.com/):

```groovy
compile 'org.thymeleaf:thymeleaf:3.0.6.RELEASE'
compile 'org.xhtmlrenderer:flying-saucer-core:9.1.6'
compile 'org.xhtmlrenderer:flying-saucer-pdf-itext5:9.1.6'
```

The project structure should look like this:

![](/images/2017/06/project.png)

## Thymeleaf Template

The Thymeleaf template which we are using is pretty simple. 
It contains a sentence and variable to fill with data. 
Note that this is a very simple example. 
Thymeleaf is much more powerful and provides concepts like **for-loops**, **if-statements** and **Java method calls** out of templates. 
You will find a lot of tutorials on the internet.
Also, there's a follow-up post which you find right [here](/generating-pdfs-with-java-flying-saucer-and-thymeleaf-part-2).

```html
<html>
    <body>
        <h1>Hi! My name is [[${name}]]!</h1>
    </body>
</html>
```

To render this template with Thymeleaf, we can use the following code. 
It will substitute the variable `${name}` with actual data. 
In the end, we will receive plain HTML which we could open in any browser.

```java
ClassLoaderTemplateResolver templateResolver = new ClassLoaderTemplateResolver();
templateResolver.setSuffix(".html");
templateResolver.setTemplateMode("HTML");

TemplateEngine templateEngine = new TemplateEngine();
templateEngine.setTemplateResolver(templateResolver);

Context context = new Context();
context.setVariable("name", "Thomas");

// Get the plain HTML with the resolved ${name} variable!
String html = templateEngine.process("template", context);
```

## PDF Generation

Generating the actual PDF is quite easy now. 
We basically put the HTML string to the PDF renderer which returns the byte stream of the generated PDF. 
The basic layout fits for our example, but we could also include some CSS in order to style the PDF.

```java
OutputStream outputStream = new FileOutputStream("message.pdf");
ITextRenderer renderer = new ITextRenderer();
renderer.setDocumentFromString(html);
renderer.layout();
renderer.createPDF(outputStream);
outputStream.close();
```

![](/images/2017/06/pdf.png)

## Advanced Topics

A lot of important topics are not covered in the very basic example above. 
However, I want to give some external links and hints on a couple of points:

- You can use CSS in order to generate page numbers with Flying Saucer as described [here](https://stackoverflow.com/questions/17953829/with-flying-saucer-how-do-i-generate-a-pdf-with-a-page-number-and-page-total-on).
- Thymeleaf templates can be divided into _fragments_. 
Those fragments can be included into other templates and provided reusable building blocks for documents (such as a header or footer). 
Read more [here](https://stackoverflow.com/questions/17953829/with-flying-saucer-how-do-i-generate-a-pdf-with-a-page-number-and-page-total-on).

## More

You find a running example on GitHub:

[https://github.com/tuhrig/Flying_Saucer_PDF_Generation](https://github.com/tuhrig/Flying_Saucer_PDF_Generation)

- [Thymeleaf](http://www.thymeleaf.org/)
- [Flying Saucer](https://github.com/flyingsaucerproject/flyingsaucer)
- [Flying Saucer, Thymeleaf and Spring](https://stackoverflow.com/questions/23173485/flying-saucer-thymeleaf-and-spring)
- [Part 2](http://tuhrig.de/generating-pdfs-with-java-flying-saucer-and-thymeleaf-part-2/) of this post for a deeper dive into styling with CSS and images.

**Best regards,** Thomas.
