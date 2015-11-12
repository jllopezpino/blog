---
layout: post
title: Python dictionaries and converting between different naming conventions
excerpt: "I shared a small gist to convert nested Python dictionaries to a different naming convention."
tags: [python, programming, data structures]
modified: 2015-11-12
comments: true
image:
    feature: old-days-of-technology.jpg
---

Yesterday, I realised that I have never shared any gist on github. Since it's about time to start, I did it with the very first script that I wrote today.


I adapted two scripts contributed to StackOverflow and created a new script that converts the keys of nested Python dictionaries to a different naming convention. These are the scripts that I used:

* [How to recursively replace the keys of a Python dictionary](http://stackoverflow.com/a/11700817/337746), which I modified to [do the same with nested dictionaries that contain lists](http://stackoverflow.com/a/33668421/337746)

* [How to convert from camel case naming convention to underscore lowercase naming convention and vice versa](http://stackoverflow.com/a/17156414/337746).

And this is the result:

{% gist 132a5cc45ea49f9f8106 %}
