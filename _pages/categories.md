---
layout: page
permalink: /categories
title: Categories
---


<div id="archives">
{% assign sortedCategories = site.categories | sort %}
{% for category in sortedCategories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>

    <h3 class="category-head">#{{ category_name }} ({{ site.categories[category_name].size }})</h3>
    <a name="{{ category_name | slugize }}"></a>
    {% assign sortedPosts = site.categories[category_name] | sort: 'title' %}
    {% for post in sortedPosts %}
    <article class="archive-item">
      <h4><a href="{{ site.baseurl }}{{ post.url }}">{% if post.title and post.title != "" %}{{post.title}}{% else %}{{post.excerpt |strip_html}}{%endif%}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>