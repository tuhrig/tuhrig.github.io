---
layout: post
title: "Writing an online scraper on Google App Engine (Python)"
date: "2013-03-30"
categories: 
  - "academic"
  - "coding"
tags: 
  - "academic"
  - "ai"
  - "data-mining"
  - "eclipse"
  - "google"
  - "programming"
  - "python"
  - "techtrends"
---

Sometimes you need to **collect data** - for visualization, data-mining, research or whatever you want. But collecting data takes time, especially when time is a major concern and data should be collected over a long period. Typically you would use a dedicated machine (e.g. a server) to do this, rather then using your own laptop or PC to crawl the internet for weeks. But setting up a server can be complicated and time consuming - nothing you would do for a small private project.

A good and **free** alternative is the **Google App Engine (GAE)**. The GAE is a web-hosting service of Google which offers a platform to upload **Java** and **Python** applications. It comes with its own user authentication system and its own database. If you already have a Google account, you can upload **up to ten applications** for free. However, the free version has some limitations, e.g. you only have a 1 GB database with a maximum of 50.000 write-operations per day (more [details](https://developers.google.com/appengine/docs/quotas "GAE quotas")).

One big advantage of the GAE is the possibility to create cron-jobs. A cron-job is a task that is executed on fixed points in time, e.g. all 10 minutes. Exactly what you need to build a scraper!

But let's do it step by step:

## 1\. Registration

First of all, you need a Google account and you must be [registered by the GAE](https://appengine.google.com/ "GAE"). After your registration, you can create a new application (go to [https://appengine.google.com](https://appengine.google.com/ "GAE") and click on _Create Application_).[

![01_gae_registration](images/01_gae_registration-300x194.png)](http://tuhrig.de/wp-content/uploads/01_gae_registration.png)

Choose the name for your application wisely, you can't change it later on!

## 2\. Install Python, GAE SDK and Google Eclipse plugin

To start programming for GAE, you need to set up some simple things. Since we want to develop an application in Python, [Python (v. 2.7) must be installed](http://www.python.org/download/ "Install Python") on your computer. Also, you need to install the [GAE SDK](https://developers.google.com/appengine/downloads "GAE Downloads") for Python. Optional, you can also install the [Google plugin for Eclipse](https://developers.google.com/eclipse/docs/getting_started "Google Eclipse plugin") together wit [PyDev](http://pydev.org/download.html "PyDev") which I would recommend, because it makes life much easier.

## 3\. Create your application

Now you can start and develop your application! Open Eclipse and create a new **PyDev Google App Engine Project**. To make a GAE application, we need at least two files: a main Python script and the `app.yaml` (a configuration file). Since we want to create a cron-job, too, we need a third file (`cron.yaml`) to define this job. For reading a RSS stream we also use a third-party library called [feedparser.py](https://code.google.com/p/feedparser/downloads/list "Feedparser"). Just download the ZIP-file and unpack the file `feedparser.py` to your project folder (this is ok for the beginning). A very simple scrawler could look like this:

### Scrawler.py

#!/usr/bin/python
# -\*- coding: utf-8 -\*-
from \_\_future\_\_ import unicode\_literals

from google.appengine.ext import webapp
from google.appengine.ext.webapp.util import run\_wsgi\_app
from google.appengine.ext import db

import feedparser 
import time

class Item(db.Model): 
    title = db.StringProperty(required=False)
    link = db.StringProperty(required=False)
    date = db.StringProperty(required=False)

class Scrawler(webapp.RequestHandler):
    
    def get(self):
        self.read\_feed()      
        self.response.out.write(self.print\_items())
        
    def read\_feed(self):
        
        feeds = feedparser.parse( "http://www.techrepublic.com/search?t=14&o=1&mode=rss" )
        
        for feed in feeds\[ "items" \]:
            querry = Item.gql("WHERE link = :1", feed\[ "link" \])
            if(querry.count() == 0):
                item = Item()
                item.title = feed\[ "title" \]
                item.link = feed\[ "link" \]
                item.date = time.strftime("%Y-%m-%d %H:%M:%S", time.gmtime(time.time()))
                item.put()
    
    def print\_items(self):
        s = "All items:  
"
        for item in Item.all():
            s += item.date + " - [" + item.title + "](" + item.link + ")  
"
        return s

application = webapp.WSGIApplication(\[('/', Scrawler)\], debug=True)

def main():
    run\_wsgi\_app(application)

if \_\_name\_\_ == "\_\_main\_\_":
    main() 

### app.yaml

application: tech-rep-scrawler
version: 1
runtime: python27
api\_version: 1
threadsafe: false

handlers:
- url: /
  script: Scrawler.py

**Note**: The `application` must be the same name as your registered on Google in the first step!

### cron.yaml

cron:
- description: scrawler
  url: /
  schedule: every 15 mins
 

Done! Your project should look like this now (including _feedparser.py_):

[![02_gae_project](images/02_gae_project-300x135.png)](http://tuhrig.de/wp-content/uploads/02_gae_project.png)

## 4\. Test it on your own machine

Before we deploy the application on GAE, we want to test it locally to see if it is really working. To do so, we have to make a new run-configuration in Eclipse. Click on the small arrow at the small green run button and choose "Run configurations...". Then, create a new "Google App Engine" configuration and fill in the following parameters (see the pictures):

**Name**: `GAE` (you can choose anything as name)

**Project**: `TechRepScrawler` (your project in your Eclipse workspace)

**Main Module**: `C:Program Files (x86)Googlegoogle_appenginedev_appserver.py` (dev\_appserver.py in your GAE installation folder)

**Program Arguments**: `--admin_port=9000 "C:UsersThomasworkspace_pythonTechRepScrawler"`

[![04_gae_run_config_1](images/04_gae_run_config_1-300x222.png)](http://tuhrig.de/wp-content/uploads/04_gae_run_config_1.png)

[![04_gae_run_config_2](images/04_gae_run_config_2-300x222.png)](http://tuhrig.de/wp-content/uploads/04_gae_run_config_2.png)

After starting GAE locally on your computer using the run configuration, just open your browser and go to [http://localhost:8080/](http://localhost:8080/ "http://localhost:8080/") to see the running application. You can also go to an admin perspective on [http://localhost:9000/](http://localhost:9000/ "http://localhost:9000/") to see, e.g. some information about your data.

## 5\. Deploy your application to GAE

The next - and last step! - is to deploy the application on GAE. Using the Google Eclipse plugin, this is as easy as it can be. Just click right on your project, go to _PyDev: Google App Engine_ and click _upload_. Now your app will be upload on GAE! On the first time, you will be asked for your credentials, that's all.

[![05_gae_upload](images/05_gae_upload-236x300.png)](http://tuhrig.de/wp-content/uploads/05_gae_upload.png)

[![06_gae_deployed](images/06_gae_deployed-300x225.png)](http://tuhrig.de/wp-content/uploads/06_gae_deployed.png)

Now your app is online and available for every one! The cron-job will refresh it every 10 minutes (which just means, it will surf on your site like every other user would do it). Here's how it should look:

[![07_last](images/07_last-300x228.png)](http://tuhrig.de/wp-content/uploads/07_last.png)

**Best regards,** Thomas Uhrig
