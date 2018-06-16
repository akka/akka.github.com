---
layout: page
title: Team
---

<h2>Core Akka Team</h2>

{% for member in site.data.team %}
  {% assign loopindex = forloop.index | modulo: 2 %}
  {% if loopindex == 1 %}
<div class="row">
  {% endif %}
<div class="sixcol">
    <div class="profile-pic-wrap"><img class="profile-pic" src="{{ member.avatar }}" /></div>
    <span class="profile-name">{{ member.name }}</span>
    <span class="profile-title">{{ member.title }}</span>
    {% if member.twitter %}
    <span class="profile-twitter-link"><a href="https://twitter.com/{{ member.twitter }}">@{{ member.twitter }}</a></span>
    {% endif %}
    <p class="profile-text">{{ member.desc }}</p>
</div>
  {% if loopindex == 0 %}
    </div>
  {% endif %}
{% endfor %}

{% assign mod = site.data.team.size | modulo:2 %}
{% if mod == 1 %}
</div>
{% endif %}

<h2>Alpakka Team</h2>

{% for member in site.data.alpakkateam %}
  {% assign loopindex = forloop.index | modulo: 2 %}
  {% if loopindex == 1 %}
<div class="row">
  {% endif %}
<div class="sixcol">
    <div class="profile-pic-wrap"><img class="profile-pic" src="{{ member.avatar }}" /></div>
    <span class="profile-name">{{ member.name }}</span>
    <span class="profile-title">{{ member.title }}</span>
    {% if member.twitter %}
    <span class="profile-twitter-link"><a href="https://twitter.com/{{ member.twitter }}">@{{ member.twitter }}</a></span>
    {% endif %}
    <p class="profile-text">{{ member.desc }}</p>
</div>
  {% if loopindex == 0 %}
    </div>
  {% endif %}
{% endfor %}

{% assign mod = site.data.alpakkateam.size | modulo:2 %}
{% if mod == 1 %}
</div>
{% endif %}

<h2>Honorary Members</h2>

{% for member in site.data.alumni_team %}
  {% assign loopindex = forloop.index | modulo: 2 %}
  {% if loopindex == 1 %}
<div class="row">
  {% endif %}
<div class="sixcol">
    <div class="profile-pic-wrap"><img class="profile-pic" src="{{ member.avatar }}" /></div>
    <span class="profile-name">{{ member.name }}</span>
    <span class="profile-title">{{ member.title }}</span>
    {% if member.twitter %}
    <span class="profile-twitter-link"><a href="https://twitter.com/{{ member.twitter }}">@{{ member.twitter }}</a></span>
    {% endif %}
    <p class="profile-text">{{ member.desc }}</p>
</div>
  {% if loopindex == 0 %}
</div>
  {% endif %}
{% endfor %}

{% assign mod = site.data.alumni_team.size | modulo:2 %}
{% if mod == 1 %}
</div>
{% endif %}

