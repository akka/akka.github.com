---
layout: page
title: Presentations and Blogs
---

Let's learn from each other! Many people have shared their experiences with Akka in presentations, blogs and webcasts. We've collected a few presentations and blogs below.

Feel free to suggest additions to this list. A great way to do that is by sharing a link to it in the [Akka forum](https://discuss.akka.io).

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
