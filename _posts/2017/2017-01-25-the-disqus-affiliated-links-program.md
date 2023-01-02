---
layout: post
title: "The Disqus Affiliated Links Program"
date: "2017-01-25"
categories:
  - "blog"
  - "wordpress"
---

Yesterday, I received an e-mail from a reader of my [blog](http://tuhrig.de). 
He told me that he clicked on the [XING](https://www.xing.com/profiles/Thomas_Uhrig7) link on my ["About"](http://tuhrig.de/about) page, but was redirected to some shady online trading website instead of my [XING](https://www.xing.com/profiles/Thomas_Uhrig7) profile. 
So I took my laptop and took a look at it. 
And - to my surprise - he was right! 
I started to investigate what was going on. 
Here's what I found out.

## What happened

My blog has an "About" page. 
Despite other things, you will find a link to my [XING](https://www.xing.com/profiles/Thomas_Uhrig7) profile there. 
The link looks like this:

```html
<a target="_blank" href="https://www.xing.com/profiles/Thomas_Uhrig7" rel="noopener">XING</a>
```

But if you clicked on the link, it will redirect you to some shady website I have never heard of.

![](/images/2017/01/wrong-link.png)

## Disqus redirects your links

Although the HTML code of the link looks absolutely simply, the user will be redirected to somewhere else. 
So I looked at the page in the [Chrome developer tools](https://developer.chrome.com/devtools). 
As you can see in the image below, the link was still correct in the HTML code. 
However, several **event listeners** have been attached to the link. 
The JavaScript file which registered the event listener came from the [Disqus](https://disqus.com) CDN.

![](/images/2017/01/chrome.png)

[Disqus](https://disqus.com) intercepted my links and redirected users to some arbitrary website.

## Affiliated Links

The reason why Disqus is doing that is called [Affiliated Links](https://en.wikipedia.org/wiki/Affiliate_marketing). 
Affiliated Links is nothing else as a way of advertisement. 
Every time a user clicks such a link, Disqus will get a small amount of money. 
That's how Disqus makes profit using your site.

## Deactivate Affiliated Links on Disqus

The bad thing about Disqus' Affiliated Links program is, that it is an **opt-out** program. 
This means if you don't say _No_, it will be activated by default. 
You must deactivate this function explicitly.

![](/images/2017/01/deactivate.png)

See: [https://help.disqus.com/customer/en/portal/articles/2425431-how-to-disable-affiliate-linking](https://help.disqus.com/customer/en/portal/articles/2425431-how-to-disable-affiliate-linking)

## No more Disqus for me

I used Disqus for the last couple of years. 
It was comfortable and worked well for me. 
However, a hidden feature like this is a dealbreaker for me. 
Although I know that a free service like Disqus must make money, manipulating my website and breaking my links is not acceptable. 
I can only guess how many people didn't find my XING or LinkedIn profile during the last years. 
I might have missed a bunch of opportunities and interesting contacts. 
**So, time to say goodbye Disqus!**

**Best regards,** Thomas.
