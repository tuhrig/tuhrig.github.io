---
layout: post
title: "Basic Auth log-out with JavaScript"
date: "2015-06-09"
categories: 
  - "coding"
  - "html"
  - "javascript"
---

Whenever you have a log-in on your site, the chance is high that you also want to have a log-out. The easiest way to do a log-in is to use HTTP's [Basic Auth](http://en.wikipedia.org/wiki/Basic_access_authentication). **But how to do a log-out with Basic Auth?**

# Basic Auth credentials are cached until the browser is closed

The problem with logging out from Basic Auth is simple: the browser will cache your credentials by default until the browser windows is closed. There is no standard mechanism to invalidate them. So Basic Auth doesn't allow a log-out!

# The dirty hack

However, there is always a dirty hack and this one goes like this: Instead of logging out, we do the opposite and try to do another login. But this time, we use a wrong user/password which will cause a `401 Unauthorized` exception in our browser. But more important, it will replace our authenticated user with the new (and wrong) user in the cached Basic Auth credentials of our browser. After that, we are effectively logged out!

function logout() {

	// To invalidate a basic auth login:
	// 
	// 	1. Call this logout function.
	//	2. It makes a GET request to an URL with false Basic Auth credentials
	//	3. The URL returns a 401 Unauthorized
	// 	4. Forward to some "you-are-logged-out"-page
	// 	5. Done, the Basic Auth header is invalid now

	jQuery.ajax({
            type: "GET",
            url: "/myapp/logout",
            async: false,
            username: "logmeout",
            password: "123456",
            headers: { "Authorization": "Basic xxx" }
	})
	.done(function(){
	    // If we don't get an error, we actually got an error as we expect an 401!
	})
	.fail(function(){
	    // We expect to get an 401 Unauthorized error! In this case we are successfully 
            // logged out and we redirect the user.
	    window.location = "/myapp/index.html";
    });

    return false;
}

# More

[http://stackoverflow.com/questions/233507/how-to-log-out-user-from-web-site-using-basic-authentication](http://stackoverflow.com/questions/233507/how-to-log-out-user-from-web-site-using-basic-authentication)

**Best regards,** Thomas
