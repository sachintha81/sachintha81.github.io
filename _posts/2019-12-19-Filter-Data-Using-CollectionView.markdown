---
layout: post
title:  "Filter data using a CollectionView with MVVM pattern"
date:   2019-12-19 11:34:14 -0700
categories: [c#, wpf, mvvm, collectionview, filtering]
---
<div class="post-categories">
  {% if post %}
    {% assign categories = post.categories %}
  {% else %}
    {% assign categories = page.categories %}
  {% endif %}
  {% for category in categories %}
  <a href="{{site.baseurl}}/categories/#{{category|slugize}}">{{category}}</a>
  {% unless forloop.last %}&nbsp;{% endunless %}
  {% endfor %}
</div>
<br>
