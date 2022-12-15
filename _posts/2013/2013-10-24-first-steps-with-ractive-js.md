---
layout: post
title: "First steps with Ractive.js"
date: "2013-10-24"
categories: 
  - "academic"
  - "coding"
  - "html"
  - "javascript"
---

During the summer I had a bit of free time. 
I had no university courses, almost no ongoing projects and was only working for two days a week. 
So I decided to build a small web app.

Since I was curiously waiting for some grades to arrive in the online portal of my university, I decided to build a (mobile) app to check this.

I wanted to build the app with Python on the server and some sort of JavaScript framework on the client. 
Many of my fellow students were talking about [AngularJS](http://angularjs.org/), but I wasn't sure if I should take such a big framework for my first steps (I never used a framework like this in JavaScript before). 
Then I stumbled over [this link on HN](https://news.ycombinator.com/item?id=6096686). 
It was about an Angluar-like framework called [Ractive.js](http://www.ractivejs.org/).

## Ractive.js

Ractive.js provides two main features (in my opinion):

1. It provides a two-way data-binding. This means that you can hand-in a JSON object and directly bind its attributes to HTML elements. So you don't have to update certain HTML elements manually (e.g. via JQuery).
2. It provides a template rendering. This means you can write HTML templates (as simple `*.html` files for example) with [mustach-syntax](http://en.wikipedia.org/wiki/Mustache_(template_system)) and render them on the client via JavaScript. To develop single-page applications gets very easy with this.

## The server part

First of all, I did the server part. 
I used a combination of [twill](http://twill.idyll.org/python-api.html) and [Beautiful Soup](http://www.crummy.com/software/BeautifulSoup/) to parse the web portal of my university. 
This was (and still is) a little bit slow, but at least no big deal. 
Then I build a small API to access these via AJAX. 
I used this API later to update my page. 
E.g. the call [http://127.0.0.1:5000/grades](http://127.0.0.1:5000/grades) would return a JSON containing all grads:

```html
{
  "grades": [
    {
      "ects": 6, 
      "grade": 1.0, 
      "name": "Des u Impl fortge Progspr", 
      "number": "38360", 
      "result": "bestanden", 
      "semester": "SS 12", 
      "subjetc": "CSM"
    },
    {
      ...
    }
}
```

I have build similar APIs for the personal schedule, for the canteen and some more.

## The client part

For the client part, I used [Pure](http://purecss.io/) (CSS framework) and (of course) Ractive.js. 
This made my templates really short and gave me a good experience on both, classical browsers and mobil. 
Here's the full template to render the grade-table:

## Grades

{% raw %}
```html
<div class="header">
    <h1>Grades</h1>
</div>
<p>ECTS: {{ects_sum}} | Mean: {{grade_mean}}</p>
<table class='grades_overview pure-table pure-table-horizontal viewer_container'>
    {{# sort(grades, sortColumn) :num}}
    <tr>
        <td>{{num+1}}</td>
        <td>{{name}}</td>
        <td>{{ects}}</td>
        <td>{{grade}}</td>
        <td class="{{result}}">{{result}}</td>
    </tr>
    {{/grades}}
</table>
```
{% endraw %}

I simply loaded the template (which is actually a `*.html` file) in my main-template. 
Afterwards I have it in a variable called `gradesTemplate` in this example:

```javascript
var gradesTemplate;
$.ajax('templates/grades.html').then(function(template) { 
    gradesTemplate = template;
});
```

Then I am able to render it, e.g. by adding an appropriate click-listener to a link. 
The listener will fetch some data via AJAX and render the templated (loaded before) with it:

```javascript
$("#grades\_tab").click(function(){
    $.getJSON('/grades')
        .done(function(data) {
            var controller = new Ractive({
                el: 'container',
                template: gradesTemplate,
                data: data
            });
        });
});
```

I'm not completely happy with using [JQuery](http://jquery.com/), but to put it all together was quite easy.

## The result

Here are some screenshot of my final version.
It's not running online and you would need an account of my university to use it, so some screenshots are the best I could provide.

![](/images/2013/10/app_4.png)
![](/images/2013/10/app_5.png)
![](/images/2013/10/app_7.png)

## The code

You can find the code on [BitBucket](https://bitbucket.org/wordless/hdm-json-api):

    git clone https://bitbucket.org/wordless/hdm-json-api.git

**Best regards**, Thomas.
