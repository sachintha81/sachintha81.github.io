---
layout: post
title:  "Sort a string, UpperCase before LowerCase"
date:   2019-08-29 13:48:00 -0700
categories: [c#, string, sort, uppercase, lowercase]
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

I came across [this interesting question](https://stackoverflow.com/questions/57716746/alphabetical-order-array-sort-doesnt-work-well?noredirect=1#comment101876508_57716746){:target="_blank"} on StackOverflow today, and thought I'd share it here.

Suppse you have a string like this: `aABcCdAcb`. I want to sort (or, 'rearrange' would be a better term here) it so that it's sorted alphabetically, and all upper case letters must come before its counterpart lower case. So, the expected output for the above string would be `AAaBbCccd`.

A `string` is an array of type `char`, so converting it into a `char` array and sorting wouldn't work since all the lower cases would come before all the upper cases. What we can try is converting it into a `string` array, and attempt to sort. I'm a big fan of `LINQ` so I'm going with that.

{% highlight c# %}
var strList = "aABcCdAcb".Select(x => x.ToString());
var sorted = string.Concat(strList.OrderBy(x => x));
{% endhighlight %}

This _almost_ gets us there, but not quite. The default sort for strings places lower case before upper case, so the resulting string would look like this, which is not quite what we want.

    aAAbBccCd
    
From here on I'll simply post the excellent answer by [Jonathan Chase](https://stackoverflow.com/users/5402620/jonathon-chase){:target="_blank"}.

Since you want to get the capitals first, we'll use the `OrdinalIgnoreCase` comparer, which will group our characters up.

{% highlight c# %}
.OrderBy(x => x, StringComparer.OrdinalIgnoreCase)
{% endhighlight %}

Okay, so we've now grouped our characters. We'll now want to sort those groups ordinally to get the capitals to come first, using `ThenBy()`.

{% highlight c# %}
.ThenBy(x => x, StringComparer.Ordinal)
{% endhighlight %}

Bringing it together, we get the following:

{% highlight c# %}
var res = str.Select(x => x.ToString())
             .OrderBy(x => x, StringComparer.OrdinalIgnoreCase)
             .ThenBy(x => x, StringComparer.Ordinal);
{% endhighlight %}

Which gives us an `IEnumerable` that is in the order `AAaBbCccd` which is what we're looking for. Now we can do a `string.Concat()` to put this back into a single `string`.

{% highlight c# %}
var resStr = string.Concat(res);
{% endhighlight %}

And we have our expected output, like so:
    `AAaBbCccd`


Another way...
--

[Rufus L](https://stackoverflow.com/users/2052655/rufus-l){:target="_blank"} has a different approach.

The problem is that your code is ordering by the `ASCII` value, which is `65-90` for uppercase `(A-Z)` and `97-122` for lowercase `(a-z)`.

Asuming you always want the uppercase letters to come before their lowercase counterparts, we can use a little trick to sort the lowercase letters just after their uppercase value by subtracting `31.5` from the `ASCII` value before doing the comparison.

By doing this, `a` becomes `65.5` `(97 - 31.5)` and will be sorted between `A` `(65)` and `B` `(66)`. Likewise for all the other lower-case letters. This avoids the cost of creating a bunch of new strings for comparison and doing multiple orderings.

{% highlight c# %}
string str = "zZYyabCABcxX";
string ordered = string.Concat(str.OrderBy(c => c > 96 && c < 122 ? c - 31.5 : c));
Console.WriteLine(ordered);
{% endhighlight %}
