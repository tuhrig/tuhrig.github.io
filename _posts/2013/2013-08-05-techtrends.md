---
layout: post
title: "TechTrends - Searching trends on HN and Reddit"
date: "2013-08-05"
categories: 
  - "academic"
  - "coding"
  - "data-mining"
  - "html"
  - "python"
---

It's done! Last Friday (26th July 2013) was the final presentation of our semester project [TechTrends](http://techtrends.mi.hdm-stuttgart.de/ "TechTrends"). Together with [Raphael Brand](https://github.com/pjuhri "Raphael Brand @ GitHub") and [Hannes Pernpeintner](https://bitbucket.org/hannespernpeintner "Hannes Pernpeintner @ Bitbucket") I worked the last 5 months on this project - and we are really happy about it.

# What is TechTrends?

TechTrends is basically a search-engine for [HN](https://news.ycombinator.com/ "HN") and [Reddit](http://www.reddit.com/r/programming/ "Reddit") (just the programming sub-reddit). You can type in a key-word and you will find a bunch of similar articles. But our main concern was not to find just articles, but also to find trends. Therefor, you will not only get a pure list of articles, you will get a chart showing when articles have been found for your query. For each article we calculate a popularity, indicating how important that article is. Based on these popularities, we draw the trend-chart for your search.

[![reddit_logo](images/reddit_logo.png)](http://www.reddit.com/r/programming/)

[![hn_logo](images/hn_logo.png)](https://news.ycombinator.com/)

# How does it work?

TechTrends has six major parts (see the graphic below). First of all, we crawl HN and Reddit all 15 minutes to get the latest links. In the second part we get the actual content form each link and store it in our database. Then we do a preprocessing on this pure text content to remove stop-words, digits and so on. After that, we use the great [Gensim Server](http://radimrehurek.com/gensim/simserver.html "Gensim Server") from Radim Řehůřek to build an index of all documents. The next (and last part on the server) is a JSON-based web API to access all of our data ([here](http://techtrends.mi.hdm-stuttgart.de/api "Web API documentation") is its documentation). On top of these API we built our user-interface - the actual website.

[![techtrends-components](images/techtrends-components-e1375687667954.png)](http://tuhrig.de/wp-content/uploads/2013/07/techtrends-components.png)

# Presentation

Here is the video of our final presentation about TechTrends in our university (on the 26the July 2013). Our presentation is about 70 minutes long and we explain a lot of details about our project. The video is available on [http://events.mi.hdm-stuttgart.de/2013-07-25-vortr%C3%A4ge-programming-intelligent-applications#techtrends](http://events.mi.hdm-stuttgart.de/2013-07-25-vortr%C3%A4ge-programming-intelligent-applications#techtrends "TechTrends Video") or below. It also contains the presentations of the other groups, but we are the first on the video.You can find the slides under the video.

# Video

Thanks to [Stephan Soller](http://arkanis.de/) and his team, our presentation has been recorded on video. You can see the video below or on [http://events.mi.hdm-stuttgart.de/](http://events.mi.hdm-stuttgart.de/).

  

# Slides

Here are the slides on my [speaker deck account](https://speakerdeck.com/tuhrig "My Speaker deck account").

https://speakerdeck.com/tuhrig/techtrends-searching-trends-on-hn-and-reddit

# What's next?

We have a lot of ideas for the future of TechTrends. We are thinking for example about a mobile version or a reporting tool. But in my oppinion, the most important step is to make TechTrends more easy to customize. Currently, we are focused on HN and Reddit. However, everything but the actual crawlers is independent of the underlying source. With a little bit of work, you can easily implement a new crawler for the data source of your choice. Making this customization more comfortable and easy is our next goal for project.

# More

There is plenty more! Here you go:

- TechTrends is online on [http://techtrends.mi.hdm-stuttgart.de/](http://techtrends.mi.hdm-stuttgart.de/ "TechTrends")
- The code is available on [https://bitbucket.org/RaBrand/techtrends](https://bitbucket.org/RaBrand/techtrends "TechTrends Code")
- The API is documented on [http://techtrends.mi.hdm-stuttgart.de/api](http://techtrends.mi.hdm-stuttgart.de/api "Web API documentation")
- All of my related posts can be found on [tuhrig.de/tag/techtrends/](tag/techtrends/ "Tag TechTrends")

**Best regards,** Thomas Uhrig
