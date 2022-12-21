---
layout: post
title: "Paper about XForms and XFormsMM"
date: "2012-05-30"
categories: 
  - "academic"
  - "coding"
  - "html"
  - "javascript"
  - "slides"
---

I have currently finished a paper about XForms (and XFormsMM) for an univerity course called [Adaptive User Interfaces](http://www.hdm-stuttgart.de/studienangebot/dozentenplaene/vorlesung_detail?vorlid=5212404 "Adaptive User Interfaces"). The paper gives a general introduction to XForms as well as to XFormsMM. XFormsMM is a (quite theoretical) approach how multi-modal forms can be build on top of XForms. It's based on a [paper](http://lib.tkk.fi/Diss/2007/isbn9789512285662/article9.pdf) of Mikko Honkala and Mikko Pohja and on the idea of extending XForms with modality-dependent style-sheets.

You can download the paper [here](http://tuhrig.de/wp-content/uploads/XForms.pdf). My own presentation about XForms can be found [here](http://tuhrig.de/?p=439 "Presentation about XForms").

I have used the following resources, which are also a good starting point to get some information about XForms:

- [Mikko Honkala & Mikko Pohja, Multimodal Interaction with XForms](http://lib.tkk.fi/Diss/2007/isbn9789512285662/article9.pdf "Multimodal Interaction with XForms"): An approach as well as a reference implementation, which shows how mulit-modal applications can be build on top of XForms.
- [Micah Dubinko, XForms Essentials](http://xformsinstitute.com/essentials/ "XForms Essentials"): A great (and free!) book, that gives a good introduction to XForms. It was published by O'Reilly 2003.
- [FormFaces](http://sourceforge.net/projects/formfaces "FormFaces on Sourceforge"): A small JavaScript framework to run XForms in your browser. It can be used "out of the box" without any installation. It's running under BSD-licence.

To get started and to get an idea of what XForms is, I've made a small example using the [FormFaces](http://sourceforge.net/projects/formfaces) library:

<iframe src="http://tuhrig.de/wp-content/uploads/xforms/questionnaire.html" width="100%" height="600" align="center" frameborder="0"></iframe>

The code is quite simple. First of all, you have to declare some namespaces for XForms itself and for the XML events XForms is using:

After that, you have to include the FormFaces JavaScript library to your code:

<script type="text/javascript" src="formfaces.js"></script>

Now you can declare an XML data-model to be modified with the form, e.g. from an XML file:

The corresponding XML file looks like this:

 123	
	false
	false	
	0	
	0 

Now, you can write XForms controls to operate on this data-model. A simple control can look like this:

**How many houres are you online per day?**
 
 Please enter a valid periode! 

So, all together:

- [The demo page (HTML + XForms + JavaScript)](http://tuhrig.de/wp-content/uploads/xforms/questionnaire.html)
- [The example data model (XML)](http://tuhrig.de/wp-content/uploads/xforms/questionnaire.xml)
- [FormFaces library @ Sourceforge.com](http://sourceforge.net/projects/formfaces)
- [My paper (PDF)](http://tuhrig.de/wp-content/uploads/XForms.pdf)
- [My presentation (PDF)](http://tuhrig.de/?p=439)

**Best regards**, Thomas Uhrig.
