---
layout: post
title: "Opinion Mining on Hackernews and Reddit"
date: "2014-01-12"
categories: 
  - "academic"
  - "coding"
  - "data-mining"
  - "python"
---

# TechTrends

Last semester two of my friends and I made some sort of a search engine for [Hackernews](https://news.ycombinator.com/) and [Reddit](http://www.reddit.com/r/programming/). The idea was to collect all articles published on those two platforms and search them for trends. It should be possible to type-in a certain keyword such as "Bitcoin" and retrieve a trend chart showing _when_ and _how many_ articles have been published to "Bitcoin".

The result was [TechTrends](http://techtrends.mi.hdm-stuttgart.de/). Based on Radim Řehůřek's [Gensim](http://radimrehurek.com/gensim/) (a Python framework for text classification) we build a web application which crawls Hackernews and Reddit continuously and offers a simple web interface to search trends in these posts. You can find more posts about TechTrends [here](/tag/techtrends/).

# OpinionTrends

This winter semester I started to implement a new feature for TechTrends. I wanted to build an opinion mining and sentiment analysis for all posts. Based on the comments for each post on [Hackernews](https://news.ycombinator.com/) and [Reddit](http://www.reddit.com/r/programming/) I wanted to classify all posts in one of three categories:

- **Positive**, which means that most of the comments are praising the article.
- **Negative**, which means that most of the comments are criticizing the article.
- **Neutral**, which means that there is a balance between positive and negative comments.

I gave it the code name **OpinionTrends**.

# The basic idea

The basic idea was to train a supervised classifier to categorize each comment and therefore each post. This should work similar to a spam filter in an email application: Each email marked as 'spam' will train a classifier which can categorize emails on its own in good or bad (which actually means spam). I wanted to do the same but with comments instead of emails and with three instead of two categories.

## Training

A classifier is only as good as its training data. In case of a spam filter the training data are emails marked as 'spam' by the user. This makes the training data very good and very individual to each user. In my case I decided to use **Amazon product reviews**.

### Amazon Product Reviews

Amazon product reviews are a great way to retrieve training data. They are marked with stars in 5 categories, they are available in many languages and for many domains and you can crawl them very easily. The only thing you have to do is to sign up a free developer account on Amazon and get started with your favorite language (there are libraries for most common languages out there).

Once the classifier is trained, it can be saved to a file and used further on. It doesn't need to be updated anymore. However, the performance of the application depends completely on the classifier. Therefore it should be trained and tested carefully.

### Validation

I tested different classifiers and different pre-processing steps of the Amazon Reviews. Below you can see a comparison between a Bayes Classifier and a SVM. The SVM beats the Bayes Classifier by 10% or more. However, its performance also depends dramatically on the pre-processing of the raw reviews.

| Type of classfier | No pre-processing | Bi-grams | Only adjectives | No stop words |
| --- | --- | --- | --- | --- |
| Bayes Classifier | 71% accuracy | 65% accuracy | 70% accuracy | 72% accuracy |
| SVM | 85% accuracy | 86% accuracy | 78% accuracy | 84% accuracy |

### Problems with validation

All tests were made with a 10-fold cross-validation. The only problem with those tests is, that I trained and tested with reviews from Amazon, but my final data were comments on blog posts which is not the exact same domain. Since [Hackernews](https://news.ycombinator.com/) and [Reddit](http://www.reddit.com/r/programming/) are both about computer science I used reviews from SSDs, Microsoft and Apple software or computer games to be as close as possible to my final domain. However, I can't really validate my final results. This has two reasons:

- I don't have a huge number of tagged blog posts and comments to compare them with the results of the classifier.
- Comments are very subjective. In many cases you can not decide for sure whether a comment is positive or negative. Some few comments are very clear and easy (_I hate your article._), but a lot of comments are something in between (_I love your website but I hate the color._). Even I as a human beeing can not decide if they are positive or negative and if I could decide it my friend would argue with me.

# My final result

OpinionTrends is in its last steps since a couple of days. Next week will present it at the [Media Night of my university](http://www.hdm-stuttgart.de/medianight) (a fairy for student projects). You can read more about it [here](http://tuhrig.de/media-night-winter-semester-20132014/).

OpinionTrends is also online and in some kind of a stable-state. However, it is still under development: [http://opiniontrends.mi.hdm-stuttgart.de/](http://opiniontrends.mi.hdm-stuttgart.de/)

## This is how it works

OpinionTrends works the same as TechTrends. You go to the website, type-in a keyword and get the results. The only really new thing is a brand new tab on the result page.

[![op_trends_new_tab](images/op_trends_new_tab.png)](http://tuhrig.de/wp-content/uploads/2014/01/op_trends_new_tab.png)

When you click on it you will see a new chart similar to the blue one on the _Timeline_ tab. The chart has three colors and is very easy to read. The green bars represent the positive articles, the red bars represent the negative articles and the light yellow bars represent the neutral articles. The neutral articles are visualized on the positive scale and on the negative scale with same amount.

[![op_trends_nsa_chart](images/op_trends_nsa_chart-e1390946392767.png)](http://tuhrig.de/wp-content/uploads/2014/01/op_trends_nsa_chart-e1390946392767.png)

Above you see the result for _NSA_, which is actually a very good example since the overwhelming opinion about the NSA is very negative which you can see perfectly in the chart.

You can click on each bar to see a pop-up showing the articles behind the bar. You can jump directly to the article or open the discussion with all comments on this article. [![op_trends_pop_up](images/op_trends_pop_up1.png)](http://tuhrig.de/wp-content/uploads/2014/01/op_trends_pop_up1.png)

## Examples

Here are some good examples to show you how OpinionTrends works. The best one I found so fare is a search for _NSA_. The opinion is very negative as everyone would expect.

[![op_trends_nsa_chart](images/op_trends_nsa_chart-e1390946392767.png)](http://tuhrig.de/wp-content/uploads/2014/01/op_trends_nsa_chart-e1390946392767.png) The opinion on _Git_ is much more balanced. It has not only a nearly equal number of positive and negative articles, it has also a lager number of neutral posts. [![op_trend_git](images/op_trend_git-e1390946354747.png)](http://tuhrig.de/wp-content/uploads/2014/01/op_trend_git-e1390946354747.png)

The opinion on _Python_ is much better. Is has a lot of neutral posts, but beside them, _Python_ has fare more positive than negative posts. [![op_trend_python](images/op_trend_python-e1390946313941.png)](http://tuhrig.de/wp-content/uploads/2014/01/op_trend_python-e1390946313941.png)

# More...

OpinionTrends has some more features such as individual settings so adjust each search. However, I think this is too much for this post. You can get a lot more information directly on the project site. TechTrends/OpinionTrends is also open source, so you can checkout the source code from [BitBucket](https://bitbucket.org/RaBrand/techtrends). **OpinionTrends is in its own branch!**

git clone https://bitbucket.org/RaBrand/techtrends.git
git fetch && git checkout opinion

I hope you enjoy it and I would be really happy about some feedback.

**Best regards**, Thomas
