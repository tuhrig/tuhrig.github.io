---
layout: post
title: "Show next image by clicking images in NextGEN Gallery"
date: "2012-10-10"
categories: 
  - "coding"
  - "javascript"
  - "blog"
---

* * *

[Here](http://tuhrig.de/show-next-image-by-clicking-images-in-nextgen-gallery-with-shutter-effect/)'s the same for NextGEN + Shutter!

* * *

[NextGEN Gallery](http://www.nextgen-gallery.com) is a great plugin for Wordpress to embed slideshows in your blog. I used it several times, you can probably find some examples below (and perhaps above if I didn't stop using it...;).

But - NextGEN Gallery comes with a very user-unfriendly default behavior: if you click on an image, the slideshow will be closed. You have to use two small buttons at the bottom of each image to navigate through the slideshow. And if the images have different sizes, you have to move your mouse after each picture, because the position of the buttons is related to the size of images.

However, since Facebook everybody will expect something different: a click on an image should show the next image and a click somewhere outside the image should close the slideshow. This enables a very easy and fast navigation through the slideshow.

To do that in NextGEN Gallery is quite simple. First of all, we have to change the used slide-effect from Shutter (the default) to Tickbox. To do this, click on _Gallery_ > _Options_ > _Effect_ and select Tickbox from the drop-down list. Now we have to modify the file thickbox.js. You can find it in ../wordpress/wp-includes/js/thickbox/thickbox.js. We have to do the following changes:

Around line 136 we have to **replace** the whole line starting like:

jQuery("#TB\_window").append("!["+caption+"]("+url+")" + "

"+caption+"

" + TB\_imageCount + TB\_PrevHTML + TB\_NextHTML + "

[![](" + tb_closeImage + ")](# ""+thickboxL10n.close+"")

");

to this:

if (!(TB\_NextHTML === "")) {

    jQuery("#TB\_window").append("!["+caption+"]("+url+")" + "

"+caption+"

" + TB\_imageCount + TB\_PrevHTML + TB\_NextHTML + "

[![](" + tb_closeImage + ")](# ""+thickboxL10n.close+"")

");
}
else {

    jQuery("#TB\_window").append("!["+caption+"]("+url+")" + "

"+caption+"

" + TB\_imageCount + TB\_PrevHTML + TB\_NextHTML + "

[![](" + tb_closeImage + ")](# ""+thickboxL10n.close+"")

");
}

Also, we have to add a new line around line 161. Under the line:

jQuery("#TB\_next").click(goNext);

we have to **add**:

jQuery("#TB\_nextPIC").click(goNext);

After that changes, you can click right on an image to navigate to the next one. If it is the last picture of the slideshow, the slideshow will be closed.

**Best regards,** Thomas Uhrig
