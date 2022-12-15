---
layout: post
title: "The thesis writing toolbox"
date: "2014-07-02"
categories: 
  - "academic"
tags: 
  - "academic"
  - "latex"
  - "lyx"
  - "writing"
---

I spend the last weeks (or months?) at my desk writing my Master thesis about [OSGi](http://www.osgi.org), PaaS and [Docker](http://www.docker.com). After my Bachelor thesis two years ago, this was my second big "literary work". And as any computer scientist, I love tools! So here is my toolbox I used to write my thesis.

# LyX/LaTeX

[![2014-06-20 14_07_02-Program Manager](images/2014-06-20-14_07_02-Program-Manager-300x193.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-14_07_02-Program-Manager.png)

I did most of my work with [LyX](http://www.lyx.org), a WYSIWYM (_what you see is what you mean_) editor for [LaTeX](http://latex-project.org). LaTeX is a _document markup language_ written by [Leslie Lamport](http://www.lamport.org) in 1984 for the type setting system [TeX](https://en.wikipedia.org/wiki/TeX) (by Donald E. Knuth). **So what does this mean?** It means that TeX is the foundation for LaTeX (and LyX). It gives you the ability to set text on paper, print words italic or leave space between two lines. But it is pretty raw and hard to handle. LaTeX is a collection of useful functions (called _macros_) to make working with TeX more pleasant. Instead of marking a certain word in bold and underline it, you could just say it is a headline and LaTeX will do the rest for you. A simple LateX document would look like this:

documentclass{article}
title{A simple document}
author{Thomas Uhrig}
title{Hello World!}
begin{document}
maketitle
section{First chapter}
Here is the actual text.
end{document}

By choosing the document class `article`, LaTeX will automatically render your text to A4 portrait paper with 10 pt font size and so on. You will not have to worry about the layout, just the content. This is how the above code will look like as a PDF:

[![2014-06-20 14_54_57-Namenlos-1.pdf - TeXworks](images/2014-06-20-14_54_57-Namenlos-1.pdf-TeXworks-300x125.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-14_54_57-Namenlos-1.pdf-TeXworks.png)

The main difference between writing your document in LaTeX instead of (e.g.) Microsoft Word, is that you do not mix content and styling. If you write with Microsoft Word, you will always see your document as the final result (e.g. a PDF) would look like. The Microsoft Word document will look the same as the PDF. This principal is called WYSIWYG (_what you see is what you get_). In LaTeX however, you will only see your raw content until you press the compile button to generate a PDF. The styling is separated from the content and only applied in the last final step when generating the PDF. This is useful, because you do not have to fight with your formatting all the time - but you have to write some pretty ugly markup.

This is where LyX comes into play. LyX is an editor to work with LaTeX, without writing a single line of code. It follows the WYSIWYM (_what you see is what you mean_) principal. This means you will see your document not in its final form, but as you mean it. Headlines will be big, bold words will be **bold** and italic word will be _italic_. Just as you mean it. However, the final styling will come in the end when generating the PDF. The LyX screenshot from above will look like this as a PDF:

[![2014-06-20 15_16_12-thesis_de.pdf - Adobe Reader](images/2014-06-20-15_16_12-thesis_de.pdf-Adobe-Reader.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-15_16_12-thesis_de.pdf-Adobe-Reader.png)

# JabRef

An important part of every thesis is literature. To organize my literature I use a tool called [JabRef](http://jabref.sourceforge.net). Its website looks very ugly, but the tool itself is really cool. It lets you generate a library with all your books and articles you want to use as references. This is my personal library for my Master thesis (with 53 entries in total):

[![2014-06-20 15_25_13-Program Manager](images/2014-06-20-15_25_13-Program-Manager.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-15_25_13-Program-Manager.png)

JabRef will generate and organize as so called BibTex file. This is a simple text file, where every book has its own entry:

@BOOK{Bakker2013,
  title = {Building Modular Cloud Apps with OSGi},
  publisher = {"O'Reilly Media, Inc."},
  year = {2013},
  author = {Bakker, Paul AND Ertman, Bert},
  address = {Sebastopol},
  isbn = {978-1-449-34513-6}
}

Every book or article I read will get its own entry in this file. I can make new entries with JabRef itself or with generators like [www.literatur-generator.de](http://www.literatur-generator.de). I can link my library to my LyX document, so every time I want to make a new reference or citation, I just open a dialog and pick the according reference:

[![2014-06-20 15_31_51-LyX_ ~_Dropbox_MA - SS14 - Master Arbeit_thesis_de.lyx](images/2014-06-20-15_31_51-LyX_-_Dropbox_MA-SS14-Master-Arbeit_thesis_de.lyx_-1024x541.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-15_31_51-LyX_-_Dropbox_MA-SS14-Master-Arbeit_thesis_de.lyx_.png)

LyX will automatically create the reference/citation and the according entry in the bibliography:

[![2014-06-20 15_34_29-thesis_de.pdf - Adobe Reader](images/2014-06-20-15_34_29-thesis_de.pdf-Adobe-Reader.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-15_34_29-thesis_de.pdf-Adobe-Reader.png)

# la­texd­iff

When you write a large document such as a thesis, you will probably make mistakes. Your university wants to have some changes and you will improve your document until its final version. However, a lot of people will not read it twice. Instead, they read it once, give you some advice and want to see the changes again. With [la­texd­iff](http://www.ctan.org/tex-archive/support/latexdiff) you can compare two LaTeX documents and create a document visualizing the changes. Here is an example from the PDF page shown above:

latexdiff v1.tex v2.tex > diff.tex

[![2014-06-20 15_41_35-diff.pdf - Adobe Reader](images/2014-06-20-15_41_35-diff.pdf-Adobe-Reader.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-15_41_35-diff.pdf-Adobe-Reader.png)

As you can see, I changed a word, inserted a comma and corrected a typo.

A great tutorial about la­texd­iff can be found at [https://www.sharelatex.com/blog/2013/02/16/using-latexdiff-for-marking-changes-to-tex-documents.html](https://www.sharelatex.com/blog/2013/02/16/using-latexdiff-for-marking-changes-to-tex-documents.html).

# Google Drive

To make graphics I use Google Drive. It is free, it is online and it is very easy. The feature I like most on Google Drive is the grid. You can align objects to each other so your drawing will look straight.

[![2014-06-20 15_54_26-Untitled drawing - Google Drawings](images/2014-06-20-15_54_26-Untitled-drawing-Google-Drawings.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-15_54_26-Untitled-drawing-Google-Drawings.png)

# Dropbox

If you loose your thesis you are screwed! Since LyX/LaTeX documents are pure text, you could easily put it into a version control system such as Git or SVN. However, you will probably use some graphics, maybe some other PDF and stuff like that. To organize this, I simple use Dropbox. Not only will Dropbox save your files for you, it also has a history. So you can easily restore a previous version of your document:

[![2014-06-20 15_49_38-Revisionen - Dropbox](images/2014-06-20-15_49_38-Revisionen-Dropbox.png)](http://tuhrig.de/wp-content/uploads/2014/06/2014-06-20-15_49_38-Revisionen-Dropbox.png)

# eLyXer - HTML export

[![elyxer](images/elyxer.png)](http://tuhrig.de/wp-content/uploads/2014/06/elyxer.png)

[eLyXer](http://alexfernandez.github.io/elyxer) is a tool to export LyX documents as HTML. Although, LyX is meant to create PDF documents in the first place, it is nice to have a HTML version to. eLyXer is already build in to LyX, so you can just export your document with some clicks:

[![2014-07-02 11_19_37-LyX_ ~_Dropbox_MA - SS14 - Master Arbeit_thesis_de.lyx](images/2014-07-02-11_19_37-LyX_-_Dropbox_MA-SS14-Master-Arbeit_thesis_de.lyx_-207x300.png)](http://tuhrig.de/wp-content/uploads/2014/07/2014-07-02-11_19_37-LyX_-_Dropbox_MA-SS14-Master-Arbeit_thesis_de.lyx_.png)

Here is the CSS I used to style the HTML output:

#globalWrapper {
	width: 100%;
	padding: 0px;
	margin-left: auto;
	margin-right: auto;
}

@media screen and (min-width: 800px) {

	#globalWrapper {
		width: 600px;
		padding: 20px;
	}
}

.Indented, .Unindented, li, .Description {
	text-align: justify;
	position: relative;
}

div .Indented, div .Unindented {
	/\* margin-top: 5px;
	margin-bottom: 5px; \*/
}

.Description-entry {
	font-size: 16px;
	font-weight:bold;
}

.tocheader {
	font-size: 36px;
}

.Part {
	margin-top: 120px;
	margin-bottom: 120px;
	font-size: 50px;
	text-align: center;
	margin-left: auto;
	margin-right: auto;
}

.Chapter, .Chapter-, .tocheader {

	margin-top: 50px;
	margin-bottom: 50px;
}

.Section {
	margin-top: 30px;
	margin-bottom: 30px;
}

.figure, .embedded, .algorithm {
	margin-top: 30px;
	margin-bottom: 30px;
	text-align: center;
	margin-left: auto;
	margin-right: auto;
	min-width: 100px;
}

pre.listing {
	margin-top: 30px;
	margin-bottom: 30px;
}

div .figure {
	max-width: 100% !important;
	padding: 0px;
}

img.figure {
	padding: 10px;
	max-width: 90% !important;
}

div .figure, div .listing, div .table, div .algorithm {
	border: none;
}

table {
	font-size: 10px;
}

.title {
	margin-top: 50px;
	margin-bottom: 50px;
	text-align: center;
	margin-left: auto;
	margin-right: auto;
}

.Subject {
	letter-spacing: 0.2em;
	font-size: 16px;
	margin-top: 35px;
	margin-bottom: 35px;
	text-align: center;
	margin-left: auto ;
	margin-right: auto ;
}

.author {
	text-align: center;
	margin-left: auto ;
	margin-right: auto ;
	font-size: 16px;
}

.Publishers {
	letter-spacing: 0.2em;
	margin-top: 60px;
	margin-bottom: 100px;
	text-align: center;
	margin-left: auto ;
	margin-right: auto ;
}

.tocindent, .toc {
	margin-top: 5px;
	margin-bottom: 5px;
}

.tocindent .tocindent .tocindent .toc  {
	text-indent: 90px;
	display: none;
}

.tocindent .tocindent .toc  {
	text-indent: 60px;
	display: none;
}

.tocindent .toc {
	text-indent: 30px;
}

.HoverFoot {
	background-color: lightgoldenrodyellow;
	position:absolute;
	bottom:0;
	left: 620px;
	width: 400px;
	font-size: 11px;
	padding: 5px;
	display: block;
	padding: 9.5px;
	margin: 0 0 10px;
	line-height: 1.42857143;
	word-break: break-all;
	word-wrap: break-word;
	color: #333;
	border: 1px solid #ccc;
	border-radius: 4px;
}

#side-nav {
	font-size: 12px;
}

img, pre.listing {
	border-radius: 3px;
	box-shadow: 0 1px 4px rgba(0,0,0,0.2);
	padding: 10px;
}

table {
	border-radius: 3px;
	box-shadow: 0 1px 4px rgba(0,0,0,0.2);
	margin-top: 30px;
	margin-bottom: 30px;
	max-width: 100% !important;
	text-align: center;
	margin-left: auto ;
	margin-right: auto ;
}

.caption {
	text-align: center;
	margin-left: auto ;
	margin-right: auto ;
}

.nomenclature, .NomenclatureEntry {
	display: none;
}

h1 {
    color: #313975 ;
    font-family: "Book Antiqua", Palatino, "Palatino Linotype", "Palatino LT STD", Georgia, serif;
    font-size: 40px;
    font-weight: normal;
    line-height: 48px;
    margin: 0 0 26px;
}

h2 {
    color: #0B1456  ;
    font-family: "Book Antiqua", Palatino, "Palatino Linotype", "Palatino LT STD", Georgia, serif;
    font-size: 26px;
    font-weight: normal;
    line-height: 36px;
    margin: 0 0 18px;
    padding: 12px 0 0;
}

h3 {
    color: #696E94 ;
    font-family: Optima, Segoe, "Segoe UI", Candara, Calibri, Arial, sans-serif;
    font-size: 16px;
    font-weight: bold;
    line-height: 18px;
    margin: 0 0 18px;
    padding: 22px 0 0;
    text-transform: uppercase;
}

.Indented, .Unindented, li, .Description {
    color: #363636;
    font-family: Optima, Segoe, "Segoe UI", Candara, Calibri, Arial, sans-serif;
    font-size: 14px;
    line-height: 18px;
    margin: 0 0 18px;
}

/\* Pagebreaks are converted into br tags which I
do not want to show. \*/
div p br {
	display: none;
}

/\* Do not display vertical space \*/
.Indented .vspace {
	display: none;
}

/\* Do not display unknown tags \*/
.Indented .unknown {
	display: none;
}

The result looks like this:

[![2014-07-02 11_24_13-Portierung einer Enterprise OSGi Anwendung auf eine PaaS](images/2014-07-02-11_24_13-Portierung-einer-Enterprise-OSGi-Anwendung-auf-eine-PaaS.png)](http://tuhrig.de/wp-content/uploads/2014/07/2014-07-02-11_24_13-Portierung-einer-Enterprise-OSGi-Anwendung-auf-eine-PaaS.png)

# The thesis writing toolbox

<table><tbody><tr><td><a href="http://www.lyx.org">LyX</a></td><td>A WYSIWYM editor for LaTeX to write documents without a single line of code.</td></tr><tr><td><a href="http://jabref.sourceforge.net">JabRef</a></td><td>An organizer for literature.</td></tr><tr><td><a href="http://www.ctan.org/tex-archive/support/latexdiff">latexdiff</a></td><td>Compares two LaTeX documents and generates a visualization of their differences.</td></tr><tr><td><a href="http://alexfernandez.github.io/elyxer">eLyXer</a></td><td>Exports LyX documents to HTML</td></tr></tbody></table>

**Best regards,** Thomas
