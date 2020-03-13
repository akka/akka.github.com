---
layout: news-main
title: External Article Archive
redirect_from:
 - "/presentations-blogs"
group: navigation
---

<div class="ninecol">
	<ul class="newsContainer">
		{% for p in site.categories.external %}
			<li>
				<a href="{{ site.baseurl }}{{ p.url }}">
					<div class="newsDate">
						<p>{{ p.date | date: "%b" }}</p>
						<p>{{ p.date | date: "%d" }}</p>
						<p>{{ p.date | date: "%Y" }}</p>
					</div>
					<div class="newsContent">
						<h1>{{ p.title }}</h1>
						<p>{{ p.content | strip_html | truncatewords: 50 }}</p>
					</div>
				</a>
			</li>
		{% endfor %}
	</ul>
</div>

{% include blog-navbar.html %}
