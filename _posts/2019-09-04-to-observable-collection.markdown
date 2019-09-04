---
layout: post
title:  "ToObservableCollection"
date:   2019-09-04 14:08:00 -0700
categories: [c#, linq, wpf, observablecollection]
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

`ToList()`, `ToArray()` etc. are great little `LINQ` methods to convert `IEnumerable`s into those respective types. Unfortunately `LINQ` doesn't provide such a method to convert one into an `ObservableCollection`, but if you've had any experience with `WPF` you know that you can't escape `ObservableCollection`s. So, here's a neat little Extension Method to do just that, so you don't have to do the extra step every time.

{% highlight c# %}
public static class ExtensionMethods
{
    public static ObservableCollection<T> ToObservableCollection<T>(this IEnumerable<T> _LinqResult)
    {
        return new ObservableCollection<T>(_LinqResult);
    }
}
{% endhighlight %}
  
##Usage##
{% highlight c# %}
var list = new List<int>() { 1, 2, 3 };
var obs = list.ToObservableCollection();
{% endhighlight %}
