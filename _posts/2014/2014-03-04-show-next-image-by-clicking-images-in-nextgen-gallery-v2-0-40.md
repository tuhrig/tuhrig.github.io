---
layout: post
title: "Show next image by clicking images in NextGEN Gallery V2.0.40"
date: "2014-03-04"
categories: 
  - "coding"
tags: 
  - "blogging"
  - "html"
  - "javascript"
  - "web"
  - "wordpress"
---

Last year I posted two modifications for the Wordpress plugin [NextGen Gallery](http://www.nextgen-gallery.com/ "NextGen Gallery") and its Thickbox and Shutter effect (here is the old [one for Thickbox](/show-next-image-by-clicking-image-in-nextgen-gallery/) and the old [one for Shutter](/show-next-image-by-clicking-images-in-nextgen-gallery-with-shutter-effect/)). The modification makes it possible to click on the current image to go forward to the next image. To close the slideshow you can just click somewhere beneath the image. This is the same behavior as on many popular website such as Facebook.

Unfortunately, NextGen Gallery released a new version of its Wordpress plugin which breaks my fix. The new version of NextGen Gallery V2.0.40 was released in November 2013 and doesn't work with my fix any more!

So here is the new fix.

# Fix for Thickbox in NextGen Gallery V2.0.40

To adjust the Thickbox effect in the current version of NextGen Gallery (V2.0.40) is quite easy. It is nearly the same fix as for the old version (only the markup for the close button is different). Here it is:

Open the file `thickbox.js` in our Wordpress installation. You can do this via FTP or with a Wordpress plugin such as [wp-FileManager](http://wordpress.org/plugins/wp-filemanager/). In both cases you will find the file in `../wordpress/wp-includes/js/thickbox/thickbox.js`. You have to do the following two changes to the file:

Around line 132 you will find the following line:

jQuery("#TB\_window").append("!["+caption+"]("+url+")" + "

"+caption+"

" + TB\_imageCount + TB\_PrevHTML + TB\_NextHTML + "

[

](# ""+thickboxL10n.close+"")

");

Replace the complete line with the following code:

if (!(TB\_NextHTML === "")) {
 
    jQuery("#TB\_window").append("!["+caption+"]("+url+")" + "

"+caption+"

" + TB\_imageCount + TB\_PrevHTML + TB\_NextHTML + "

[

](# ""+thickboxL10n.close+"")

");
}
else {
 
    jQuery("#TB\_window").append("!["+caption+"]("+url+")" + "

"+caption+"

" + TB\_imageCount + TB\_PrevHTML + TB\_NextHTML + "

[

](# ""+thickboxL10n.close+"")

");
}

Now you have to add a new line around line 161. Under the line:

jQuery("#TB\_next").click(goNext);

you have to add:

jQuery("#TB\_nextPIC").click(goNext);

Save the file. That's it.

# Fix for Shutter in NextGen Gallery V2.0.40

Open the file `shutter.js` in our Wordpress installation. You can do this via FTP or with a Wordpress plugin such as [wp-FileManager](http://wordpress.org/plugins/wp-filemanager/). In both cases you will find the file in `../wordpress/wp-content/plugins/nextgen_gallery/products/photocrati_nextgen/modules/lightbox/static/shutter/shutter.js`. You have to do the following two changes to the file:

Around line number 143 there should be a line like this:

D.innerHTML = '

![]('+shutterLinks[ln].link+' "' + t.msgClose + '")' + NavBar +'

';

Replace the complete line with the following code:

D.innerHTML = '

![]('+shutterLinks[ln].link+' "' + t.msgClose + '")' + NavBar +'

';

Now you have to add a new function just before this code around line 153:

loading : function() {...

Add this function to the file:

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

Note that the comma at the end is important! Now the file should look like this:

[![NextGEN-Shutter-Mod](images/2013-11-13-10_49_33-wordpress-Microsoft-WebMatrix.png)](http://tuhrig.de/wp-content/uploads/2013/11/2013-11-13-10_49_33-wordpress-Microsoft-WebMatrix.png)

Save the file. That's it. And by the way, this is the same fix as for the old version!

# Download the files

I know that fixing some JavaScript files is ugly, so here are the modified files. Just replace the original one:

[shutter.js](http://tuhrig.de/wp-content/uploads/2014/03/shutter.js) (replace `../wordpress/wp-content/plugins/nextgen_gallery/products/photocrati_nextgen/modules/lightbox/static/shutter/shutter.js`)

[thickbox.js](http://tuhrig.de/wp-content/uploads/2014/03/thickbox.js) (replace `../wordpress/wp-includes/js/thickbox/thickbox.js`)

**Best regards**, Thomas
