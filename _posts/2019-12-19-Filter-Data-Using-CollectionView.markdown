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

You have a `DataGrid` control that displays a bunch of data, and suppose that you want to do some filtering on it. What's the best way to go about it?

Perhaps the simplest and most obvious way to do this is by creating two lists. One holds all the data, and when you want to filter, you create a subset of that data according to your filtering criteria, and we bind that list to the `DataGrid`, so the updated (by which I mean filtered) data will be displayed. But you probably already noticed the big flaw of this approach; data duplication. It's quite fine for few thousand lines, but suppose you're handling lists that contain few hundred thousands, or even millions of items. Now that data duplication can potentially be a burden on your system. Besides, there's a fundamental problem with this approach anyway; merely to _show_ a subset of data, you shouldn't have to _create_ a subset of data.

Luckily, we don't have to do that. `.NET` provides a neat way to do this using what's called a `CollectionView`. The basic idea is this; we have only one list of data, but we create a `CollectionView` and we _link_ the list to it. Then we attach a `Filter` to this `CollectionView`, which essentially is a method inside which we determine, based on whatever logic we wish, whether an item should be displayed or not.  Finally, when we want to filter data, we simply tell the `CollectionView` to go filter data based on that method.

So... let's get started.

For the purpose of this, let's say we have a contact list, and we want to filter contacts based on age. My `XAML` is going to be very simple: a `DataGrid` with two columns. At the bottom, two text boxes, `From` and `To` which lets user enter the minimum and maximum ages for the filter. Finally a button, which, when clicked, does the filtering. I bind my `Contacts` list to the `DataGrid`, and rest of the bindings should be obvious. For this example I'm using `MVVM` with `MVVM Light Toolkit`, hence `RelayCommand` and such you'll see later are part of that suite.

