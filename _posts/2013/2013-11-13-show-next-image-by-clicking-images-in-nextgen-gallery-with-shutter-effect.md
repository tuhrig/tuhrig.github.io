---
layout: post
title: "Show next image by clicking images in NextGEN Gallery with shutter effect"
date: "2013-11-13"
categories: 
  - "coding"
tags: 
  - "blogging"
  - "html"
  - "javascript"
  - "picture"
  - "shutter"
  - "ui"
  - "web"
  - "wordpress"
---

* * *

[Here](http://tuhrig.de/show-next-image-by-clicking-image-in-nextgen-gallery/)'s the same for NextGEN + Thickbox!

* * *

Last year I posted an article about modifying NextGEN Gallery (with Thickbox) to show the next image when you click on the current one. This behaviour is well known from Facebook an co. To go to the next picture, just click on the current one. To close the slideshow, just click beside the picture. Easy. You can find the article [here](http://tuhrig.de/show-next-image-by-clicking-image-in-nextgen-gallery/).

Since I used the Thickbox effect in my modification, a lot of people asked me if it is possbile with the Shutter effect, too. Of course it is!

Here's what you have to do to modify NextGEN Gallery with the Shutter effect to display the next image by clicking on the current one. I tested it with Wordpress 3.7.1 und NextGen 2.0.33.

1 - Open the file `wp-content/plugins/nextgen_gallery/products/photocrati_nextgen/modules/lightbox/static/shutter/shutter.js`

2 - Arround line number 143 there should be a line like this:

D.innerHTML = '

![]('+shutterLinks[ln].link+')

You have to replace this line with the following one:

D.innerHTML = '

![]('+shutterLinks[ln].link+' "' + t.msgClose + '")' + NavBar +'

';

3 - Now you have to add a new function just before 

loading : function() {...

Here's the function you have to add:

lastCall : undefined,
nextOrClose : function(next) {
    if(next !== undefined) {
        shutterReloaded.make(next);
    }
    else if(shutterReloaded.lastCall === undefined){
        shutterReloaded.hideShutter();
    }
    shutterReloaded.lastCall = next;
},

Now the file should look like this:

[![NextGEN-Shutter-Mod](images/2013-11-13-10_49_33-wordpress-Microsoft-WebMatrix-1024x514.png)](http://tuhrig.de/wp-content/uploads/2013/11/2013-11-13-10_49_33-wordpress-Microsoft-WebMatrix.png)

That's it! I hope it works for everyone.

**Best regards**,
Thomas
