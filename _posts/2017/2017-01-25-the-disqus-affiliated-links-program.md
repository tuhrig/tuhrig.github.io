---
layout: post
title: "The Disqus Affiliated Links Program"
date: "2017-01-25"
categories: 
  - "coding"
tags: 
  - "affiliated-links"
  - "blogging"
  - "disqus"
  - "javascript"
  - "wordpress"
coverImage: "crawl-1076324_1280.jpg"
---

Yesterday, I received an e-mail from somebody who read an article on [my blog](http://tuhrig.de). He told me that he clicked on the [XING](https://www.xing.com/profiles/Thomas_Uhrig7) link on [my "About" page](http://tuhrig.de/about), but was redirected to some shady online trading website instead of my [XING](https://www.xing.com/profiles/Thomas_Uhrig7) profile. So I took my laptop and reviewed my own blog, immediately. And - OMG - he was right! I started to investigate what was going on. Here's what I found out.

# What happened

My blog has an "About" page. Despite other things, you will find a link to my [XING](https://www.xing.com/profiles/Thomas_Uhrig7) profile there. The link looks like this:

[XING](https://www.xing.com/profiles/Thomas_Uhrig7)

But if you clicked on the link, you will be redirected to some shady website I have never heard of.

[![](images/Untitled-drawing-5.png)](http://tuhrig.de/wp-content/uploads/2017/01/Untitled-drawing-5.png)

# Disqus redirects your links

Although the HTML code of the link looks absolutely simply, the user will be redirected to somewhere else. So I looked at the page in the [Chrome developer tools](https://developer.chrome.com/devtools). As you can see in the image below, the link was still correct in the HTML code. However, several **event listeners** have been attached to the link. The JavaScript file which registered the event listener came from the [Disqus](https://disqus.com) CDN.

[![](images/Untitled-drawing-6.png)](http://tuhrig.de/wp-content/uploads/2017/01/Untitled-drawing-6.png)

This means [Disqus](https://disqus.com) will intercept your links and might redirect users to some arbitrary website.

# Affiliated Links

The reason why [Disqus](https://disqus.com) is doing that is called [Affiliated Links](https://en.wikipedia.org/wiki/Affiliate_marketing). Affiliated Links is nothing else as a way of advertisement. Every time an user clicks such a link, Disqus will get a small amount of money. That's how Disqus will make money by using your site.

# Deactivate Affiliated Links on Disqus

The bad thing about Disqus' Affiliated Links program is, that it's an **opt-out** program. This means if you don't say "No!", it will be activated automatically. You must deactivate this function explicitly.

[![](images/Untitled-drawing-7.png)](http://tuhrig.de/wp-content/uploads/2017/01/Untitled-drawing-7.png)

See: [https://help.disqus.com/customer/en/portal/articles/2425431-how-to-disable-affiliate-linking](https://help.disqus.com/customer/en/portal/articles/2425431-how-to-disable-affiliate-linking)

# No more Disqus for me

I used [Disqus](https://disqus.com) for the last couple of years. It was quite comfortable and was working well for me. However, a hidden feature like this is a deal breaker for me. Although I know that a free service like [Disqus](https://disqus.com) must make some money, manipulating my website and breaking my links is not acceptable. I can only guess how many people didn't find my XING or LinkedIn profile during the last years. I might lost a lot of opportunities and interesting contacts. **So, time to say good by Disqus!**

**Best regards,** Thomas
