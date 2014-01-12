---
layout: page
title: Archive
permalink: /archive/
---


<h3>This year's posts</h3>

{%for post in site.posts %}
{% unless post.next %}
<dl class="dl-horizontal">
{% else %}
    {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
    {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
{% if year != nyear %}
</dl>

---

<h3>Posts from {{ post.date | date: '%Y' }}</h3>

<dl class="dl-horizontal">
{% endif %}
{% endunless %}
    <dt>
        {{ post.date | date:"%e %b" }}
    </dt>
    <dd>
        <a href="{{ post.url }}">{{ post.title }}</a>
    </dd>
{% endfor %}
</dl>
