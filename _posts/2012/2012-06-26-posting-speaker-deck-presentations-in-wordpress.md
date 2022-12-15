---
layout: post
title: "Posting Speaker Deck Presentations in Wordpress"
date: "2012-06-26"
categories: 
  - "coding"
tags: 
  - "blogging"
  - "php"
  - "plugin"
  - "wordpress"
---

I've recently tried to post a [Spreaker Deck](https://speakerdeck.com/) presentation on my Wordpress blog. But a problem using Google's Chrome browser came up to me: Chrome throws a scripting exception when I post the embedded code.

[![](images/chrome_error.png "chrome_error")](http://tuhrig.de/wp-content/uploads/chrome_error.png)

**The exception:**

Refused to execute a JavaScript script. Source code of script found within request.

**The embedded code:**

<script async class="speakerdeck-embed" data-id="4fe79de5558e2d00220135d9" data-ratio="1.3333333333333333" src="//speakerdeck.com/assets/embed.js"></script>

Chrome seems to refuse the execution of the script - perhaps to prevent something like cross-site-scripting. But the solution is pretty simple. First, install a new [plugin](http://wordpress.org/extend/plugins/speakerdeck-embed) to your wordpress blog to embed Speacker Deck presentations. Then fix it by changing the method to:

wp\_oembed\_add\_provider(
    '#http(s)?://speakerdeck.com/u/.\*/p/.\*#i', 
    'http://speakerdeck.com/oembed.json', 
    true 
);

[Here](http://wordpress.org/support/topic/plugin-speaker-deck-embed-add-https-support)'s the post for the fix. Thanks!

**Plugin:** [http://wordpress.org/extend/plugins/speakerdeck-embed](http://wordpress.org/extend/plugins/speakerdeck-embed)

**Fix:** [http://wordpress.org/support/topic/plugin-speaker-deck-embed-add-https-support](http://wordpress.org/support/topic/plugin-speaker-deck-embed-add-https-support)

**Best regards,** Thomas Uhrig
