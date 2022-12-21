---
layout: post
title: "Install OpinionTrends with nginx and memcached"
date: "2014-01-22"
categories: 
  - "academic"
  - "coding"
  - "data-mining"
  - "python"
---

[OpinionTrends](http://OpinionTrends.mi.hdm-stuttgart.de/) is build with [Python](http://www.python.org/) and [Flask](http://flask.pocoo.org/). Therefore you can run it without any additional server right out of the box. Batteries included! However, it is much more common and much more efficient if you run it with a web server and an application server. A widely used combination for Python web applications is [nginx](http://nginx.org/) together with [uWSGI](http://projects.unbit.it/uwsgi/). In this tutorial I want to show how we set up this two tools to run [TechTrends](http://techtrends.mi.hdm-stuttgart.de/) and [OpinionTrends](http://opiniontrends.mi.hdm-stuttgart.de/). The tutorial starts at the very beginning and the only thing I assume is that you run a Linux machine with [Python](http://www.python.org/) and [easy\_install](https://pypi.python.org/pypi/setuptools).

**Note:** OpinionTrends is the new version of TechTrends. However, it is just a code name. So when I talk about OpinionTrends or TechTrends I mean the same thing.

# OpinionTrends

## Base folder

The first thing we have to do is to create the base directory for TechTrends. By convention a web application is located in `/var/www`. All of our code will go into this folder.

mkdir /var/www/techtrends

## Clone & Update

Now we can start to set-up the application from scratch. OpinionTrends is deployed with `git`. So we just check-out the code and switch to the new `opinion` branch:

git clone https://bitbucket.org/RaBrand/techtrends.git
git fetch && git checkout opinion

After the first checkout an update is super easy. Just do a `pull` for the latest code:

git pull

## Configuration

Now we have to make some simple settings for TechTrends. To do this, we create a new file called `config.py` in the folder `Configuration` in our checked-out project. There is also a file called `config_template.py` in this folder which is an empty but well documented template for the configuration. The file contains all individual settings for the application. It should look like this:

import logging
class Config:
    DB\_FILE = '/var/www/links.sqlite'
    CORPUS\_FILE = '/var/www/corpus.sqlite'
    SIMILARITY\_SERVER = '/var/www/simserver'
    CACHE = '/var/www/cache.txt'
    CLASSIFIER = '/var/www/classifier.pkl'
    DEBUG = False
    PORT = 80
    HOST = "0.0.0.0"
    METHOD = 'lsi'
    RESPECT\_ROBOTS\_TXT = 'True'
    AMAZON\_CREDENTIALS = ""
    TWITTER\_CREDENTIALS = ""

## Dependencies

Now we have to install the libraries needed by TechTrends. To do this we use [easy\_install](https://pypi.python.org/pypi/setuptools). All dependencies are in a file called `requirements.txt` in the root folder of TechTrends. We have to install all dependencies in this file:

easy\_install requirements.txt

**Note:** As you see, the installation of the dependencies is very easy in theory. However, some dependencies such as `scikit-learn` are sometimes hard to install since they are not pure Python and use come C bindings. If you have problems installing the whole `requirements.txt` at once try to install every dependency manually on its own.

# nginx

[![800px-Nginx_Logo.svg](images/800px-Nginx_Logo.svg_-e1390944284938.png)](http://tuhrig.de/wp-content/uploads/2014/01/800px-Nginx_Logo.svg_-e1390944284938.png)

## Install

Installing nginx is not a big thing:

sudo apt-get install nginx
sudo /etc/init.d/nginx start

After we installed and started nginx we can verify if it is running correctly by taking our favorite browser and surf to our machine.

By the way, stopping nginx is also very simple:

/etc/init.d/nginx stop

## Configuration

Now we have to configure ngix to point to TechTrends instead to the default welcome page. To do this we first remove the default configuration:

sudo rm /etc/nginx/sites-enabled/default

Now we create our own configuration in `/var/www/techtrends/nginx.conf`. It should look like this:

server {
    listen       80;
    server\_name  \_;
    location / { try\_files $uri @yourapplication; }
    location /static {
        root /var/www/techtrends/WebServer;
        expires 7d;
        add\_header Pragma public;
        add\_header Cache-Control "public, must-revalidate, proxy-revalidate";
    }
    location @yourapplication {
      include uwsgi\_params;
      uwsgi\_pass unix:/var/www/techtrends/uwsgi.sock;
    }
}

We link this file to nginx a restart it:

sudo ln -s /var/www/techtrends/nginx.conf /etc/nginx/conf.d/
sudo /etc/init.d/nginx restart

Now we should get a `Bad Gateway` exception. Perfect! This tells use that nginx found our configuration and that every things looks good - except of the missing uWSGI!

# uWSGI

[![logo_uWSGI](images/logo_uWSGI.png)](http://tuhrig.de/wp-content/uploads/2014/01/logo_uWSGI.png)

## Install

uWGSI is the protocol between our Python application and nginx. It is their way of communication. First we have to install it:

pip install uwsgi

## Configuration

Now we create a configuration file in `/var/www/techtrends/uwsgi.ini`. It should look like this:

\[uwsgi\]
plugins = python
base = /var/www/techtrends
app = start\_webserver
module = %(app)
socket = /var/www/techtrends/%n.sock
chmod-socket    = 666
callable = app
logto = /var/log/uwsgi/%n.log

Now we can start uWSGI as a daemon in the background:

uwsgi --ini /var/www/techtrends/uwsgi.ini --daemonize uwsgi\_daemon.log

Done! Nginx is serving TechTrends now. However, we should get an exception again since memcached is still missing. If we want to use TechTrends without memcache we have to change a value in the `config.py` in the `Configuration` folder in TechTrends base directory. We have to set `DEBUG = True` to _not_ use memcache.

# memcached

[![memcached_banner75](images/memcached_banner75-e1390944737740.jpg)](http://tuhrig.de/wp-content/uploads/2014/01/memcached_banner75-e1390944737740.jpg)

One of the biggest performance improvements you can do (I guess in general, but especially for TechTrends) is to use [memcached](http://memcached.org/). Memcached is a key-value in-memory store to cache frequently requested data. In TechTrends we use it to store whole pages and JSON responses from our API.

## Install

Install memcached first:

sudo apt-get install memcached

And that's it! Memcached is installed and running now. You can restart memcached (e.g. to clear it) like this:

sudo /etc/init.d/memcached restart

## Congratulations

Congratulations! TechTrends should run now in a stable production mode. We installed nginx, uWSGI and memcached. We also configured it to work together. Great! But - there are still some open points we have to do.

# crontab

TechTrends/OpinionTrends has two regularly scheduled jobs. One job is the crawler which crawls posts from [Reddit](http://www.reddit.com/r/programming/) and [Hackernews](https://news.ycombinator.com/) and the other job is the training and restart of the application. To execute this jobs we set-up a cron tab. First we create two files which execute these jobs. The first file is called `crawl.sh` and looks like this:

python /var/www/techtrends/start\_scraper.py > /var/www/techtrends/crontab\_scraper.txt

The second file is called `restart.sh` and looks like this:

rm -r /var/www/simserver
python /var/www/techtrends/start\_training.py > /var/www/techtrends/crontab\_training.txt
pkill -9 -f start\_daemon.py
nohup python /var/www/techtrends/start\_daemon.py > /var/www/techtrends/nohup\_daemon.txt &
/etc/init.d/memcached restart
killall uwsgi
uwsgi --ini /var/www/techtrends/uwsgi.ini --daemonize uwsgi\_daemon.log

Both files should be in the root folder (`/var/www/techtrends/`) of TechTrends. Now we add those two files to our locale `crontab`. We can edit the it like this:

crontab -e

It should look like this:

PYTHONPATH=:/var/www/techtrends:/var/www/techtrends/Configuration:/var/www/techtrends/Database:/var/www/techtrends/Maintenance:/var/www/techtrends/Opinion:/var$

# m h  dom mon dow   command
\*/30 \* \* \* \* cd /var/www/techtrends; sh crawl.sh
0 3 \* \* \* cd /var/www/techtrends; sh restart.sh

This `crontab` will run the crawler every 30 minutes and once a day at 3 o'clock it will trigger the training and restart of the whole application. So the data will growth every 30 minutes and will be indexed once a day. That's it. **Best regards**, Thomas
