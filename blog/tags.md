---
layout: page
title: All Articles by Tag
header: All Articles by Tag
group: navigation
---
{% include JB/setup %}

<div class="wrapper">
	<div class="row">
		<ul class="tag_box inline">
  		{% assign tags_list = site.tags %}  
  		{% include JB/tags_list %}
		</ul>

		{% for tag in site.tags %} 
  		<h2 id="{{ tag[0] }}-ref">Tag: {{ tag[0] }}</h2>
  		<ul>
    		{% assign pages_list = tag[1] %}  
    		{% include JB/pages_list %}
  		</ul>
		{% endfor %}
	</div>
</div>