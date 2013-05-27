---
layout: page
title: "Projects"
description: ""
group: navigation
---
{% include JB/setup %}

<ul>
{% for page in site.pages %}
{% if page.group == 'project' %}
  <li><a href="{{ page.url }}">{{ page.title }}</a></li>
{% endif %} 
{% endfor %}
</ul>
