---
layout: page
permalink: /about/index.html
title: ABOUT ME
tags: [Shivam, Dixit, me, shivamd001]
imagefeature: cover6.jpg
chart: true
---
<figure>
  <img src="{{ site.url }}/images/shivam-dixit.jpg" alt="Shivam Dixit">
  <figcaption>Shivam Dixit</figcaption>
</figure>

{% assign total_words = 0 %}
{% assign total_readtime = 0 %}
{% assign featuredcount = 0 %}

{% for post in site.posts %}
    {% assign post_words = post.content | strip_html | number_of_words %}
    {% assign ert = post_words | divided_by:180 %}
    {% assign ertremainder = post_words | modulo:180 %}
        {% if ertremainder >= 90 %}
            {% assign readtime = ert | plus:1 %}
        {% else %}
            {% assign readtime = ert %}
        {% endif %}
    {% assign total_words = total_words | plus: post_words %}
    {% assign total_readtime = total_readtime | plus: readtime %}
    {% if post.featured %}
    {% assign featuredcount = featuredcount | plus: 1 %}
    {% endif %}
{% endfor %}

I'm **Shivam Dixit**, pre-final year undergraduate student and web developer. During the daytime you would find me glued to my laptop, at night I fight ninjas. I love to explore new technologies and always looking forward for challenging problems.

I'm pursuing **B.Tech** in Computer Science and Engineering from LNM-IIT, India. I've been working with a software company as a __student employee__ since 2013. I was selected in Google Summer Of Code 2014 in my sophomore year. I've been actively contributing to open source softwares since then. I love to break things, especially web applications and have reported security vulnerabilities in various web applications.

###Resume
Download the latest version <a href="{{ site.url }}/assets/resume.pdf">here</a>. Personal information has been removed from the online version of the resume.

###Blog Stats

This blog currently has {{ site.posts | size }} posts in {{ site.categories | size }} categories which combinedly have {{ total_words }} words, which will take an average reader approximately {{ total_readtime }} minutes to read. {% if featuredcount != 0 %}There are <a href="{{ site.url }}/featured">{{ featuredcount }} featured posts</a>, you should definitely check those out.{% endif %} The most recent post is {% for post in site.posts limit:1 %}{% if post.description %}<a href="{{ site.url }}{{ post.url }}" data-toggle="tooltip" title="{{ post.description }}">"{{ post.title }}"</a>{% else %}<a href="{{ site.url }}{{ post.url }}" data-toggle="tooltip" title="{{ post.description }}" data-toggle="tooltip" title="Read more about {{ post.title }}">"{{ post.title }}"</a>{% endif %}{% endfor %} which was published on {% for post in site.posts limit:1 %}{% assign modifiedtime = post.modified | date: "%Y%m%d" %}{% assign posttime = post.date | date: "%Y%m%d" %}<time datetime="{{ post.date | date_to_xmlschema }}" class="post-time">{{ post.date | date: "%d %b %Y" }}</time>{% if post.modified %}{% if modifiedtime != posttime %} and last modified on <time datetime="{{ post.modified | date: "%Y-%m-%d" }}" itemprop="dateModified">{{ post.modified | date: "%d %b %Y" }}</time>{% endif %}{% endif %}{% endfor %}. The last commit was on {{ site.time | date: "%A, %d %b %Y" }} at {{ site.time | date: "%I:%M %p" }} [UTC](http://en.wikipedia.org/wiki/Coordinated_Universal_Time "Temps Universel Coordonné").
