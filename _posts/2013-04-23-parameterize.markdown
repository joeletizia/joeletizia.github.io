---
layout: post
title:  "Parameterize - URLs Don't Like to be Slashed"
date:   2013-04-23 09:00:00
categories: jekyll update
---
I was recently working on a side project to create an RSS reader in Rails. Well, to be honest, it was more of a javascript application that simply consumed data from a rails service. You can checkout the project here.

One thing I had to do was send a unique identifier to the front end to identify the article to read. To do this, I encoded the URL text in base64. I thought this would work great...until I realized that the '/' character is in the base64 character set...shit.

But fear not...rails has a nice little function in ActiveSupport called `parameterize` that will format a string into a URL-safe string.

[Checkout the gist][github]


[github]: https://gist.github.com/joeletizia/5431527
