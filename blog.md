---
layout: page
permalink: /blog/
crumbs: 
- text: Home
  url: /
title: Blog
---
<section id="blog" class="bg-light-gray">
	<div class="container">
		<div class="row">
            <div class="col-lg-12 text-center">
                <h2 class="section-heading">Blog Posts</h2>
                <h3 class="section-subheading text-muted">Project updates.</h3>
            </div>
        </div>
		<ul class="post-list">
			{% for post in site.posts %}
				<li>
					<span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>

					<h2>
						<a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
					</h2>
					<p>{{ post.description }}</p>
				</li>
			{% endfor %}
		</ul>
	</div>
</section>
