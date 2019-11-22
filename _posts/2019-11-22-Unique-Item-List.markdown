---
layout: post
title:  "Unique Item List"
date:   2019-11-22 14:14:14 -0700
categories: [c#, list, IEquatable]
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

Suppose you want a list of stuff. Well, lucky for you, `.NET` offers just what you want; a list.

{% highlight c# %}
var list = new List<int> { 1, 2, 3 };
{% endhighlight %}

All good, but what if you want a list that will only hold unique items. `HashSet` will get the job done then; it'll only add an item if it's unique. So, if we redfine our 'list' to be this:

{% highlight c# %}
var list = new HashSet<int> { 1, 2, 3 };
{% endhighlight %}

The following will add `4` to our `list`.
{% highlight c# %}
list.Add(4);	
{% endhighlight %}

But it will return `false` below, and will not add `2` to the list again.

{% highlight c# %}
list.Add(2);	
{% endhighlight %}

Do more
--
So far so good. But let's take it up a notch. What if we want a list not of a base type like `int`, but a list of some class instances. Say we have a `SpaceCowboy` class with a unique `ID` property and a non-unique `Name` property. What if we want to add unique instances of `SpaceCowboy` based on the `ID` property. If we use the same code as before, unfortunately, the `HashSet` will happily add both items below, although they have identical `ID`s.

{% highlight c# %}
var list = new HashSet<SpaceCowboy>();
list.Add(new SpaceCowboy() { ID = 1, Name = "Malcolm Reynolds" });
list.Add(new SpaceCowboy() { ID = 1, Name = "Malcolm Reynolds" });
{% endhighlight %}

That's because the _uniqueness_ is decided by using the default `IEqualityComparer`. In other words, it will use `IEqualityComparer<T>.GetHashCode()` to decide if an item is unique or not. Therefore, it will add both items above, since as soon as you did `new SpaceCowboy()`, it generated a unique instance in the eyes of the `HashSet` although its property values are the same. <a href="https://stackoverflow.com/a/8952026/302248" target="_blank">This is why</a> it'll return `true` for both above `Add()` calls, but will return `false` for the second `Add()` below.

{% highlight c# %}
var list = new HashSet<Customer>();
var sc = new SpaceCowboy() { ID = 1, Name = "Malcolm Reynolds" };
list.Add(sc);
list.Add(sc);
{% endhighlight %}

So... what then?
--
We can explicitly tell the `HashSet` to take `ID` into account when deciding uniqueness. We do this by implementing `IEqutable<T>` on the class in question. We tell it what it needs to take into account when deciding uniquness, and override `GetHashCode()` method.

{% highlight c# %}
public class SpaceCowboy : IEquatable<SpaceCowboy>
{
    public int ID { get; set; }
    public string Name { get; set; }

    public bool Equals(SpaceCowboy sc)
    {
        if (ID == sc.ID)
            return true;
        else
            return false;
    }

    public override int GetHashCode()
    {
        return ID;
    }
}
{% endhighlight %}

Now it will only add the first of the following two items and return `false` for the second.

{% highlight c# %}
var list = new HashSet<SpaceCowboy>();
list.Add(new SpaceCowboy() { ID = 1, Name = "Malcolm Reynolds" });
list.Add(new SpaceCowboy() { ID = 1, Name = "Malcolm Reynolds" });
{% endhighlight %}

Stuff we can do
--
Implementing `IEquatable<T>` actually allows you to do much more than adding stuff to a `HashSet`, because that's what other `IEnumerable`s use as well. So, if you want to use a regular `List<T>` and use `List<T>.Contains()` to add only the unique items by `ID`, now you can.

{% highlight c# %}
var list = new List<SpaceCowboy>();
var sc1 = new SpaceCowboy() { ID = 1, Name = "Malcolm Reynolds" };
var sc2 = new SpaceCowboy() { ID = 1, Name = "Inara Serra" };

if (!list.Contains(sc1))
    list.Add(sc1);
if (!list.Contains(sc2))
    list.Add(sc2);
{% endhighlight %}

In the above code snippet, it'll only add the first object.

You can even use a `Dictionary<TKey, TValue>` with it now. Say you have a `Dictionary<SpaceCowboy, int>` (I have no idea what the `int` will stand for in this context, but hey, give me a break), and, obviouly, you want the `Dictionary` to add items where `ID` is unique.

{% highlight c# %}
var dict = new Dictionary<SpaceCowboy, int>();
dict.Add(new SpaceCowboy() { ID = 1, Name = "Inara Serra" }, 100);
dict.Add(new SpaceCowboy() { ID = 1, Name = "Kaylee Frye" }, 200);
{% endhighlight %}

Above, the first line will add the instance, but in the second it will throw an `Exception` saying the `Key` already exists.

So, there you have it. Use your newfound power wisely.
