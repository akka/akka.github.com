---
layout: news-main
title: News & Articles
---

<div class="ninecol">
	<div class="box selArticle">
		{% assign mostRecent = site.posts.first %} 
		<div class="selArticleHeader">
			<div class="date">
				<p>{{ mostRecent.date | date: "%B %d"}}</p>
				<p>{{ mostRecent.date | date: "%Y"}}</p>
			</div>
			<h1><a href="{{ site.baseurl }}{{ mostRecent.url }}">{{ mostRecent.title }}</a></h1>
		</div>
		<div class="selArticleContent">
			{{ mostRecent.content }}
		</div>
	</div>
</div>
{% include blog-navbar.html %}
