---
layout: post
title: "Extracting meaningful content from raw HTML"
date: "2013-05-07"
categories: 
  - "academic"
  - "coding"
  - "data-mining"
  - "html"
  - "python"
---

Parsing HTML is easy. Libraries like [Beautiful Soup](http://www.crummy.com/software/BeautifulSoup/ "Beautiful Soup") give you an compact and straight forward interface to process websites in your preferred programming language. But this is only the first step. The interesting question is: **How to extract the meaningful content of HTML?**

I tried to find a answer to this questions during the last couple of days - and here's what I found.

# Arc90 Readability

My favorite solution is the so called **Arc90 Readability algorithm**. It was developed by the [Arc90 Labs](http://lab.arc90.com/2009/03/02/readability/ "Arc90 Labs") to make websites more comfortable to read (e.g. on mobile devices). You can find it - for example - as a [Google Chrome browser plugin](https://chrome.google.com/webstore/detail/readability/oknpjjbmpnndlpmnhmekjpocelpnlfdi "Google Chrome Readabilit plugin"). The whole project is also on [Google Code](https://code.google.com/p/arc90labs-readability/ "Arc90 on Google Code"), but more interesting is the actual algorithm, ported to Python by [Nirmal Patel](http://nirmalpatel.com/ "Nirmal Patel"). Here you can find his original [source code](http://nirmalpatel.com/fcgi/hn.py "http://nirmalpatel.com/fcgi/hn.py").

The algorithm is based on two lists of HTML-ID-names and HTML-CLASS-names. One list contains IDs and CLASSes with a **positive meaning**, the other list contains IDs and CLASSes with a **negative meaning**. If a tag has a positive `ID` or `CLASS`, it will get additional points; if it has a negative `ID` or `CLASS`, it will loos points. When we calculate this points for all tags in the HTML document, we can just render the tags with the most points to get the main content in the end. Here's an example:

# My post

...

Contact

The first `div`\-tag has a very positive `ID` (_"id="post"_), so it will probably contain the actual post. However, the `div`\-tag in the second line has a very negative `class` (_class="footer"_), which tells use that it seems to contain the footer of the page and not any meaningful content. With this knowledge, we do the following:

1. `get all paragraphs (p-tags) from the HTML source`
2. `for each paragraph:`
    1. `add the parent of the paragraph to a list (if it's not already added)`
    2. `initialize the score of the parent with 0`
    3. `if the parent has a positive attribute, add points!`
    4. `if the parent has a negative attribute, subtract points!`
    5. `optional: check additional rules, e.g. a minimum length`
3. `find parent with most points (the so called top-parent)`
4. `render the textual content of the top-parent`

Here's my code which is based very much on the code of [Nirmal Patel](http://nirmalpatel.com/ "Nirmal Patel") which you can find [here](http://nirmalpatel.com/fcgi/hn.py "http://nirmalpatel.com/fcgi/hn.py"). The main thing I changed, is some more cleaning before the actual algorithm. This will produce an easy to interpret HTML without scripts, images and so on, but still with all textual content.

import re
from bs4 import BeautifulSoup
from bs4 import Comment
from bs4 import Tag

NEGATIVE = re.compile(".\*comment.\*|.\*meta.\*|.\*footer.\*|.\*foot.\*|.\*cloud.\*|.\*head.\*")
POSITIVE = re.compile(".\*post.\*|.\*hentry.\*|.\*entry.\*|.\*content.\*|.\*text.\*|.\*body.\*")
BR = re.compile("  
\[ rn\]\*  
")

def extract\_content\_with\_Arc90(html):

    soup = BeautifulSoup( re.sub(BR, "

", html) )
    soup = simplify\_html\_before(soup)

    topParent = None
    parents = \[\]
    for paragraph in soup.findAll("p"):
        
        parent = paragraph.parent
        
        if (parent not in parents):
            parents.append(parent)
            parent.score = 0

            if (parent.has\_key("class")):
                if (NEGATIVE.match(str(parent\["class"\]))):
                    parent.score -= 50
                elif (POSITIVE.match(str(parent\["class"\]))):
                    parent.score += 25

            if (parent.has\_key("id")):
                if (NEGATIVE.match(str(parent\["id"\]))):
                    parent.score -= 50
                elif (POSITIVE.match(str(parent\["id"\]))):
                    parent.score += 25

        if (len( paragraph.renderContents() ) > 10):
            parent.score += 1

        # you can add more rules here!

    topParent = max(parents, key=lambda x: x.score)
    simplify\_html\_after(topParent)
    return topParent.text

def simplify\_html\_after(soup):

    for element in soup.findAll(True):
        element.attrs = {}    
        if( len( element.renderContents().strip() ) == 0 ):
            element.extract()
    return soup

def simplify\_html\_before(soup):

    comments = soup.findAll(text=lambda text:isinstance(text, Comment))
    \[comment.extract() for comment in comments\]

    # you can add more rules here!

    map(lambda x: x.replaceWith(x.text.strip()), soup.findAll("li"))    # tag to text
    map(lambda x: x.replaceWith(x.text.strip()), soup.findAll("em"))    # tag to text
    map(lambda x: x.replaceWith(x.text.strip()), soup.findAll("tt"))    # tag to text
    map(lambda x: x.replaceWith(x.text.strip()), soup.findAll("b"))     # tag to text
    
    replace\_by\_paragraph(soup, 'blockquote')
    replace\_by\_paragraph(soup, 'quote')

    map(lambda x: x.extract(), soup.findAll("code"))      # delete all
    map(lambda x: x.extract(), soup.findAll("style"))     # delete all
    map(lambda x: x.extract(), soup.findAll("script"))    # delete all
    map(lambda x: x.extract(), soup.findAll("link"))      # delete all
    
    delete\_if\_no\_text(soup, "td")
    delete\_if\_no\_text(soup, "tr")
    delete\_if\_no\_text(soup, "div")

    delete\_by\_min\_size(soup, "td", 10, 2)
    delete\_by\_min\_size(soup, "tr", 10, 2)
    delete\_by\_min\_size(soup, "div", 10, 2)
    delete\_by\_min\_size(soup, "table", 10, 2)
    delete\_by\_min\_size(soup, "p", 50, 2)

    return soup

def delete\_if\_no\_text(soup, tag):
    
    for p in soup.findAll(tag):
        if(len(p.renderContents().strip()) == 0):
            p.extract()

def delete\_by\_min\_size(soup, tag, length, children):
    
    for p in soup.findAll(tag):
        if(len(p.text) < length and len(p) <= children):
            p.extract()

def replace\_by\_paragraph(soup, tag):
    
    for t in soup.findAll(tag):
        t.name = "p"
        t.attrs = {}  

# Whitespace Rendering

The idea behind this technique is quite simple and goes like this: You go trough your raw HTML string and replace every tag (everything between `<` and `>`) with white spaces. When you render the content, all textual blocks should still be "blocks", whereas the rest of the page should be scattered words with a lot of white spaces.The only thing you have to do right now is to get the blocks of text and throw away the rest. Here's a quick implementation:

#!/usr/bin/python
# -\*- coding: utf-8 -\*-

import requests
import re

from bs4 import BeautifulSoup
from bs4 import Comment

if \_\_name\_\_ == "\_\_main\_\_":
    
    html\_string = requests.get('http://www.zdnet.com/windows-8-microsofts-new-coke-moment-7000014779/').text
    
    soup = BeautifulSoup(str( html\_string ))
    
    map(lambda x: x.extract(), soup.findAll("code"))
    map(lambda x: x.extract(), soup.findAll("script"))
    map(lambda x: x.extract(), soup.findAll("pre"))
    map(lambda x: x.extract(), soup.findAll("style"))
    map(lambda x: x.extract(), soup.findAll("embed"))
    
    comments = soup.findAll(text=lambda text:isinstance(text, Comment))
        
    \[comment.extract() for comment in comments\]
    
    white\_string = ""
    isIn = False;
    
    for character in soup.prettify():

        if character == "<":
            isIn = True;
        
        if isIn:
            white\_string += " "
        else:
            white\_string += character
            
        if character == ">":
            isIn = False;
            
    for string in white\_string.split("           "):    # tune here!
        
        p = string.strip()
        p = re.sub(' +',' ', p)
        p = re.sub('n+',' ', p)
        
        if( len( p.strip() ) > 50):
            print p.strip()

The problem with this solution is, that it's not very generic. You have to do a lot of fine tuning to find a good length of white space to split the string. Websites with a lot of markup will produce much more white spaces compared to simple pages. On the other side, this is a quite simple approach - a simple is mostly good.

# Libraries

As always: don't reinvent the wheel! There are a lot of libraries out there that are dealing with this problem. On of my favorite libraries is **Boilerpipe**. You can find it online as a web-service on [http://boilerpipe-web.appspot.com/](http://boilerpipe-web.appspot.com/ "http://boilerpipe-web.appspot.com/") and as a Java project on [https://code.google.com/p/boilerpipe/](https://code.google.com/p/boilerpipe/ "https://code.google.com/p/boilerpipe/"). It's doing a real good job, but compared to the two algorithms I explained above, it's much more complicated inside. However, using it as a black-box might be a good solution to find your content.

**Best regards,** Thomas Uhrig
