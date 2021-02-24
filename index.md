---
layout: page
title: hello
---

![messy desk](/images/desk.jpg)

### what am i up to

{% for post in site.posts limit:1 %}
#### [{{ post.title }}]({{ post.url }})

{{ post.date | date: "%b %-d, %Y" }}

{{ post.excerpt }}

{% endfor %}

[more...](/log/)

### contact

I may be reached at s at shh dot sh . My GPG Key is 1BE6 766C DC52 439A 5722 DCA2 BDE4 3806 8A2B D353.

You can also interact with me on [Twitter](https://www.twitter.com/worldwise001).

Check out the [cat(s)](https://www.instagram.com/shh_furbabies).
