---
layout: default

---

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

{% for categories in site.categores %}
	<h3>{{ categores[0] }}</h3>
	<u1>
		{% for post in categores[1] %}
		<li><a href="{{ post.url }}">{{ post.title }}</a></li>
		{% endfor %}
	</u1>
{% endfor %}