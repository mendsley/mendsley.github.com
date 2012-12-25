---
layout: default
title: Learning to #Gamedev
description: Thoughts on my adventures building video games
---

Contact
-------
<div id="profile">
<table><tr><td markdown="1">
![Profile](/images/profile_sm.png)
</td><td markdown="1">
[@MatthewEndsley](https://twitter.com/#!/MatthewEndsley)  
[https://github.com/mendsley](https://github.com/mendsley)  
</td></tr></table>
</div>

Posts and stuff
===============

<ul class="posts">
	{% for post in site.posts %}
		<li>
			<span>{{ post.date | date_to_string }}</span>
			&raquo;
			<a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title}}</a>
		</li>
	{% endfor %}
</ul>


Current Projects
================
[https://carbongames.com](https://carbongames.com)  
[http://airmech.com](http://airmech.com)
