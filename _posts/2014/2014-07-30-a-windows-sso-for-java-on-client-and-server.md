---
layout: post
title: "A Windows SSO (for Java on client and server)"
date: "2014-07-30"
categories: 
  - "coding"
tags: 
  - "google"
  - "informatica"
  - "java"
  - "security"
  - "sso"
  - "windows"
---

A couple of months ago I worked on a [single sign-on](http://en.wikipedia.org/wiki/Single_sign-on) (SSO) for a Windows client and server made in Java. The scenario was the following:

- A client made with Java running on Windows
- A server made with Java running on Windows
- Both where logged-in to the same domain (an Active Directory LDAP)

The question was, how the server could get the identity (the name of the Windows account) of the client and - of course - how it could trust this information. But if the client would just send a name (e.g. from Java's `System.getProperty("user.name");` method), the client could send anything.

		|------------------|                      |------------------|
		|   << Client >>   |                      |   << Server >>   |
		|                  | <=== get identity == |                  |
		|       Java       |                      |       Java       |
		|      Windows     |  === user name ===>  |      Windows     |
		|                  |                      |                  |
		|------------------|                      |------------------|

		|------------------------------------------------------------|
		|           Company domain (an Active Directory LDAP)        |
		|------------------------------------------------------------|

The solution for this dilemma (trust what the client sends to you) is to use a (so called) [trusted third party](http://en.wikipedia.org/wiki/Trusted_third_party). A trusted third party is an instance which both, client and server, know and trust. The client authenticates itself to this party and the server can verify requests against it. In the scenario above, the domain of the company (an Active Directory LDAP) is the trusted third party. Each client identifies itself against this domain when it logs-in to Windows. Its Windows username and password are checked by the domain/LDAP. On the other side, the server has also access to the domain controller and can verify information send by the client.

The nice thing about this is, that the Windows domain is already configured on nearly every machine which stands in a company. Every company, bigger than maybe five people, will have a Windows domain to log-in. Therefor, a SSO based on the Windows domain will work right out of the box in most cases and we don't need and configuration in our Java code, since it is already configured in Windows.

# Java Native Access (JNA)

To use Windows and the domain controller for authentication, we can use native Windows APIs. To call those APIs in Java, we can use the [Java Native Access (JNA)](https://github.com/twall/jna) library, which you can find on GitHub at [https://github.com/twall/jna](https://github.com/twall/jna) and on Maven central:

 net.java.dev.jna
    platform
    3.5.2 

For example, to get all user groups of the current user, you would do:

import com.sun.jna.platform.win32.Advapi32Util;
import com.sun.jna.platform.win32.Advapi32Util.Account;
public class UserGroupPrinter {
    public static void main( String\[\] args ) {
    	for(Account account: Advapi32Util.getCurrentUserGroups())
    	    System.out.println(account.fqn);
    }
}

# Waffle

![](images/waffle.jpg)

On top of JNA exists a library called Waffle which encapsulates all functionality you need to implement user authentication. You can find it on GitHub at [https://github.com/dblock/waffle](https://github.com/dblock/waffle) and also on Maven central:

 com.github.dblock.waffle
    waffle-jna
    1.5 

You can use Waffle to create a token on the client, send it to the server (e.g. over HTTP or whatever) and to validate that token on the server. At the end of this process (create, send and validate) you will know on the server who the client is - for sure!

Here is an example of how to identify a client on the server. Note that this piece of code is executed completely on one machine. However, you could easily split it into two parts, one on the client and one on the server. The only thing you would need to do, is to exchange the `byte[]` tokens between client and server. I commented the appropriate lines of code.

(By the way, I asked this myself on [Stackoverflow](http://stackoverflow.com/questions/17918344/what-is-this-waffle-sso-example-doing) some times ago).

import com.sun.jna.platform.win32.Sspi;
import com.sun.jna.platform.win32.Sspi.SecBufferDesc;

import waffle.windows.auth.IWindowsSecurityContext;
import waffle.windows.auth.impl.WindowsAuthProviderImpl;
import waffle.windows.auth.impl.WindowsSecurityContextImpl;

public class Authenticator {

	public static void main(String args\[\]) {

		// initialize a security context on the client
		IWindowsSecurityContext clientContext = WindowsSecurityContextImpl.getCurrent( "Negotiate", "localhost" );
		
		// create an auth provider and a security context for the client 
		// on the server
		WindowsAuthProviderImpl provider = new WindowsAuthProviderImpl();
		IWindowsSecurityContext serverContext = null;

		// now you would send the byte\[\] token to the server and the server will 
		// response with another byte\[\] token, which the client needs to answer again
		do {  	
			
			// Step 2: If you have already build an initial security context for the client
			// on the server, send a token back to the client, which the client needs to 
			// accept and send back to the server again (a handshake)
		    if (serverContext != null) {
		        byte\[\] tokenForTheClientOnTheServer = serverContext.getToken();
		    	SecBufferDesc continueToken = new SecBufferDesc(Sspi.SECBUFFER\_TOKEN, tokenForTheClientOnTheServer);
		        clientContext.initialize(clientContext.getHandle(), continueToken, "localhost");
		    }  

		    // Step 1: accept the token on the server and build a security context 
		    // representing the client on the server
		    byte\[\] tokenForTheServerOnTheClient = clientContext.getToken();
		    serverContext = provider.acceptSecurityToken("server-connection", tokenForTheServerOnTheClient, "Negotiate");

		} while (clientContext.isContinue());

		// at the end of this handshake, we know on the server side who the
		// client is, only by exchanging byte\[\] arrays
		System.out.println(serverContext.getIdentity().getFqn());
	}
}

The only thing that is a little bit complicated with that solution is, that you need to do a small handshake between client and server. The client will send a token to the server, which will response with another token, which the client needs to answer again to get the final "you are authenticated" token from the server. To do this, you need to hold some state on the server for the duration of the handshake. Since the handshake is done in a second or two, I just used a [limited cache from Google's Guava library](https://code.google.com/p/guava-libraries/wiki/CachesExplained) to hold maybe 100 client contexts on the server.

		|------------------|                      |------------------|
		|   << Client >>   |   == 1. token ==>    |   << Server >>   |
		|                  |   <== 2. token ==    |                  |
		|                  |   == 3. token ==>    |                  |
		|                  |   <== 4. token ==    |                  |
		|------------------|                      |------------------|

The exchanged tokens are validated against the underlying Windows and its domain.

**Best regards,** Thomas
