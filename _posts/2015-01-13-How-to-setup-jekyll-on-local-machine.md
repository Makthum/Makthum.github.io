---
layout: post
published: true
title: Setup Jekyll on your local Machine
mathjax: false
featured: true
comments: true
headline: Blogging using Github pages + Jekyll
categories: 
  - webdevelopment
tags: jekyll
---
Why Jekyll
============

In my last term , I told myself when i am done with my masters i am setting up github pages for myself. Hurray there you go I have done some googling and have setup my github pages using Jekyll. It is way better than static github pages and you can also blog using jekyll. I should thank @hmfaysal for letting him use his Jekyll theme for free. Lets get started.


Github Pages
============

Yes github allows users to setup pages for projects as well as it users. For users you need to create a new repo with a repository name as "yourname".github.io without quotes. If you are planning to project page then you must setup a gh-pages branch and load all your project page files in that branch. I am interested only in user page. 


Jekyll Installation 
===================

If you are not planning to blog or maintain the posts in future you can directly fork the Jekyll themes from any of the github repositories and give it a go. Your page should be up in a minute. I wanted to blog regularly so i wanted a local setup to write and publish post to my github repository. For local dev setup you need to install Jekyll first. Github doesn't support all the gems for now it supports only two gems. You should check their doc page for more details [here](https://pages.github.com) and [Github + Jekyll](https://help.github.com/articles/using-jekyll-with-pages/)

{% highlight ruby %}
gem install jekyll
{% endhighlight %}

Once you have installed Jekyll . you can clone the jekyll theme repo to start with. This guy has some excellent [Themes](https://github.com/hmfaysal). You can use git clone command to fetch the theme.

Now open the terminal and change your working directory to the cloned theme directory.

{% highlight ruby %}
jekyll server --watch 
{% endhighlight %}

This shud start the dev server and you can hit the localhost:4000 to very your blog on your local machine. You can customize the theme starting with config.yml. you can change all the setttings such as title, name , email ... etc. Screenshot below shows some of the settings.

![config.yml]({{% site.url %}}/images/Configyml.png)


This is just a tip of an iceberg. if you are good at html,css and javascript then options are limitless. As part of the theme i already have bootstrap and jquery setup. This should make things interesting for people who wants to add some animations to your page. 
Smashing Magazine also have some good tutorial on jekyll blogging . [link](http://www.smashingmagazine.com/2014/08/01/build-blog-jekyll-github-pages/)
