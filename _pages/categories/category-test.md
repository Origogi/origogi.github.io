---
title: "Test"
layout: archive
permalink: categories/test
author_profile: true
sidebar_main: true
---


{% include group-by-array collection=site.posts field="Test" %}
{% for category in group_names %}
  {% if category[0] == "Android" or  category[0] == "Kotlin"%}
    {% assign posts = group_items[forloop.index0] %}
    <h2 id="{{ category | slugify }}" class="archive__subtitle">{{ category }}</h2>
    {% for post in posts %}
        {% include archive-single.html %}
    {% endfor %}
  {% endif %}



{% endfor %}