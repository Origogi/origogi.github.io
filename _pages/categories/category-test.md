---
title: "Test"
layout: archive
permalink: categories/test
author_profile: true
sidebar_main: true
---
{% assign posts = site.categories.Flutter  %}
  <h2 class="archive__subtitle">Flutter</h2>
{% for post in posts %} 
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}