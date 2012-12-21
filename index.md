---
layout: default
---

Contact
-------
<div id="profile">
<table><tr><td markdown="1">
![Profile](/images/profile_sm.png)
</td><td markdown="1">
[@MatthewEndsley](https://twitter.com/#!/MatthewEndsley)  
[https://github.com/mendsley](https://github.com/mendsley)  
[https://carbongames.com](https://carbongames.com)  
[https://airmech.com](https://airmech.com)
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
