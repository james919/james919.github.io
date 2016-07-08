---
title: Archive
layout: single
excerpt: "Archive of posts on James Wright's blog."
sitemap: true
permalink: /archive/
---
## Blog Posts

{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}