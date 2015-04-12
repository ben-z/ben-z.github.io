---
layout: page
title: Archive
permalink: /archive/
---

{% for post in site.posts  %}
  {% capture this_year %}{{ post.date | date: "%Y" }}{% endcapture %}
  {% capture this_month %}{{ post.date | date: "%B" }}{% endcapture %}
  {% capture next_year %}{{ post.previous.date | date: "%Y" }}{% endcapture %}
  {% capture next_month %}{{ post.previous.date | date: "%B" }}{% endcapture %}

  {% if forloop.first %}
##{{this_year}}
###{{this_month}}
  {% endif %}

* {{ post.date | date: "%B %e, %Y" }} &raquo; [{{ post.title }}]({{ BASE_PATH }}{{ post.url }})

  {% if forloop.last %}
    </ul>
  {% else %}
    {% if this_year != next_year %}
##{{next_year}}
###{{next_month}}
    {% else %}
      {% if this_month != next_month %}
###{{next_month}}
      {% endif %}
    {% endif %}
  {% endif %}
{% endfor %}
