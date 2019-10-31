---
layout: page
title: Presentations and Blogs
---

Many people have reported about their work with Akka in different forms. Find a few presentaions, blogs and articles below.

<section class="wrapper indexLatest">
    <div class="row">
        <h1>Presentations</h1>
    </div>
    <div class="row">
        <ul class="newsContainer">
{% for p in site.data.presentations_blogs %}
  {% if p.type contains "presentation" %}
    {% include presentations-blogs-item.html %}
  {% endif %}
{% endfor %}
        </ul>
    </div>

    <div class="row">
        <h1>Blogs</h1>
    </div>
    <div class="row">
        <ul class="newsContainer">
{% for p in site.data.presentations_blogs %}
  {% if p.type contains "blog" %}
    {% include presentations-blogs-item.html %}
  {% endif %}
{% endfor %}
        </ul>
    </div>

</section>
