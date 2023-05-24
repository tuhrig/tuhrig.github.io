---
layout: post
title: "A basic micro-frontend with Vaadin"
date: "2023-05-23"
categories: 
  - "coding"
  - "java"
  - "spring"
  - "vaadin" 
---

Microservices are a well established pattern in backend development.
Everybody is using it. 
At [Bringmeister](https://www.bringmeister.de/), we're running more than two dozens microservices just to handle product data alone.
But when it comes to frontend development, things are often different.
I experienced two situations a lot:

 - The frontend is a big blob. A single application, developed and deployed as one large package.
 - Every service has its own little frontend, but they are not connected in any way. 

The second was the case for our internal admin UIs at Bringmeister.
Every other service has its own little UI and everybody maintains a list of bookmarks to find things again.

This post shows a simple approach to solve this problem. 
You will find the following below:

 - Build a simple Spring Boot app with Vaadin
 - Integrate multiple (Vaadin) UIs via IFrames
 - Communicate between IFrames via `window.postMessage()`
 - Discussion of various aspects and alternative approaches

Although this example uses a certain tech-stack ([Spring Boot](https://spring.io/projects/spring-boot), [Vaadin](https://vaadin.com/), [Kotlin](https://kotlinlang.org/)), 
the shown principles are simple and technology-agnostic.

## Spring Boot with Vaadin

Setting up a Spring Boot app with Vaadin is really simple. 
We can use the Spring Initializr at [https://start.spring.io](https://start.spring.io) to setup the basic project skeleton. 

![](/images/2023/05/spring-init.png)

After that, we can create a simple Vaadin view and we are almost done with the first step.

````kotlin
@Route("")
class SimpleVaadinView : VerticalLayout() {
    init {
        this.add(Html("<h1>Hello!</h1>"))
    }
}
````

However, there's a small but important detail for the example we want to implement.
Our final goal is, to have multiple Vaadin apps running in the same browser tab via IFrames.
All of those apps will run under the same host (`localhost`).
So we must ensure two things:

 - ⚠️ Every Vaadin app gets its own unique port
 - ⚠️ The `JSESSIONID` cookie must have a unique name

We can achieve both by using the `application.properties`:

````properties
server.port=8080
server.servlet.session.cookie.name=JSESSIONID_MY_SIMPLE_VIEW
````

Having a unique port is obvious as you cannot run multiple applications on the same port.
Renaming the `JSESSIONID` is necessary, because every app has its own session.
And since all apps will run in the same tab and under the same host, the cookie would be overwritten.
This would result in expired sessions, because only the last `JSESSIONID` would be stored in the cookie.
By renaming the cookie, we ensure that every app can handle its sessions correctly.

## Integration via IFrames

We want to integrate multiple independent UIs on a single page.
The example we want to implement looks like this:

```
   +=======================================================+
   |                    << Browser Tab >>         x - *    |
   +=======================================================+
   |   Main-View                                           |
   |                                                       |
   |         << IFrame >>             << IFrame >>         |
   |   +---------------------+   +---------------------+   |
   |   | Left-View           |   | Right-View          |   |
   |   |                     |   |                     |   |
   |   |                     |   |                     |   |
   |   |                     |   |                     |   |  
   |   +---------------------+   +---------------------+   |
   |                                                       |
   +=======================================================+
```

We want to implement three independent apps:

 - A **left-view** which will run on port `8081` 
 - A **right-view** which will run on port `8082` 
 - And a **main-view** which will run on port `8080` and which will integrate the other views 

For the example (which you can find on [GitHub](https://github.com/tuhrig/micro-ui-with-vaadin)) we implement the following:

- The **left-view** shows a list of programming languages.
  The user can click on a language and select it.
  <img src="/images/2023/05/left.png"  width="400">

- The **right-view** shows a short description of a programming language.
  The user cannot click anything. 
  The language can only be selected using a URL parameter (like `http://localhost:8082/languages/Kotlin`)
  <img src="/images/2023/05/right.png"  width="400">

- The **main-view** finally includes the other views via IFrames.
  It also provides a nice heading on top of it.
  <img src="/images/2023/05/main.png"  width="400">

Doing this is quite simple. 
The **main-view** looks like this:

````kotlin
@Route("languages")
class MainVaadinView : VerticalLayout(), HasUrlParameter<String?> {

    private val heading = Html("<h1>Choose a programming language!</h1>")
    private val leftIFrame = IFrame("http://localhost:8081/languages")
    private val rightIFrame = IFrame("http://localhost:8082/languages")

    init {

        val splitLayout = SplitLayout(leftIFrame, rightIFrame)
        splitLayout.setSizeFull()

        this.add(heading)
        this.add(splitLayout)
        this.setSizeFull()
    }

    override fun setParameter(event: BeforeEvent, @OptionalParameter language: String?) {
        if (!parameter.isNullOrBlank()) {
            heading.setHtmlContent("<h1>What is ${language}?</h1>")
            leftIFrame.src = "http://localhost:8081/languages/$language"
            rightIFrame.src = "http://localhost:8082/languages/$language"
        }
    }
}
````

What do we have right now?

 - The **main-view** integrates both other views via IFrames
 - Depending on the URL parameter, a language is selected (e.g. `/languages/Java`)
 - The language is passed on to the other views by setting the `src`  of the IFrame accordingly

However, the important part is still missing: the interaction.
A click on the **left-view** should change what the **right-view** is showing.
How can we achieve this?

## Communication between IFrames

Usually, IFrames are isolated and protected by the [same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy).
Only code from the same origin (protocol + host + port) can interact.

**However, there's an exception:** 
We can use `window.postMessage()` (see [here](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)) for cross-origin communication. 
If we obtain a reference to a `window` object, this API enables us to post a message to the `window`.
The `window` in return can listen to the event and react accordingly.

The basic idea goes like this:

````javascript
// The Main-View has a listener for messages
window.addEventListener("message", (event) => {
  console.log(event.data)
});

// The IFrame can post a message to its parent
window.top.postMessage("Hello there!", "*");
````

Based on this simple approach, we can implement a communication pattern between the IFrames:

![](/images/2023/05/communication.png)

1. The user selects something in the left IFrame. 
By using `window.top.postMessage(...)` the IFrame can send an event to its parent (the `top` window).
Note that it is not possible to send a message directly to the other IFrame since there is no reference to this `window` object. 
2. The parent IFrame has a `window.addEventListener` to listen for the event. 
The event has some predefined format which is the protocol between the IFrames.
This can be anything, for example: `{"language":"Kotlin"}`.
3. The parent IFrame broadcasts the event to all of its children. 
It is the only place where we can obtain a reference to all `window` objects.
4. Every (child) IFrame can handle or ignore the event as it wants to.

![](/images/2023/05/flow.gif)

As you can see, communication between IFrames only requires a bit of vanilla JavaScript.
But in case of Vaadin, we need an additional step, because all logic resides on the server-side. 
So we must transfer the JavaScript events back to the server in order to handle them.

To do so, we can use Vaadin's `@ClientCallable` annotation (see [here](https://vaadin.com/docs/latest/create-ui/element-api/client-server-rpc/#clientcallable-annotation)).
It lets us implement a listener method to send data from the JavaScript frontend to the Kotlin backend.

````kotlin
@ClientCallable
fun receiveFrontendEvent(event: String) {
    log.info("Received event from frontend: {}", event)
    ...
}
````

We can hook this method with some JavaScript to our `window.addEventListener`:

````kotlin
element.executeJs("""
    window.addEventListener("message", (event) => {
        ${'$'}0.${'$'}server.receiveFrontendEvent(event.data);
    });
""".trimIndent(), element)
````

See the [example on GitHub](https://github.com/tuhrig/micro-ui-with-vaadin) for the complete implementation.

## Discussion

- **Are IFrames bad?** - IFrames are just another tool in the box.
They are simple, provide good isolation (especially in [sandbox](https://www.w3schools.com/tags/att_iframe_sandbox.asp) mode) and are designed to embed content to a page.
The `window.postMessage(...)`-API makes communication save and easy. 
For simple micro-frontends with a decent amount of embedded elements, they are a good choice in my opinion.
- **Any alternatives?** - You can find a discussion on different approaches at [martinfowler.com](https://martinfowler.com/articles/micro-frontends.html).
Besides [IFrames](https://martinfowler.com/articles/micro-frontends.html#Run-timeIntegrationViaIframes), the article lists server-side techniques and web-components.
- **Any specialties for Vaadin?** - Vaadin provides a `WebComponentExporter` (see [here](https://vaadin.com/docs/v14/flow/integrations/embedding/tutorial-webcomponent-exporter)) to export web-components.
You can find an example right [here](https://vaadin.com/labs/micro-frontend). 
A drawback for me is the need for a shared JavaScript bundle to use in the browser.

## More

- [https://github.com/tuhrig/micro-ui-with-vaadin](https://github.com/tuhrig/micro-ui-with-vaadin)
- [https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)
- [https://martinfowler.com/articles/micro-frontends.html](https://martinfowler.com/articles/micro-frontends.html)

**Best regards,** Thomas.
