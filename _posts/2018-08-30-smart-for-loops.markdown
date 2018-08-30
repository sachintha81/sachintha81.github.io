---
layout: post
title:  "Smart For-Loops"
date:   2018-08-30 14:23:00 -0700
categories: [c#, programming practices, for-loop, iteration]
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

The `Average Joe` For-Loop
--
We have a method and we want to execute it a certain number of times. Say, 10. We can easily write a `for-loop` that iterates `10` times and it's all good.

#### The Normal Way  
{% highlight c# %}
static void Main(string[] args)
{
    for (int i = 0; i < 10; i++)
    {
        SayHello();
    }
}

public static void SayHello()
{
    Console.WriteLine("Hello!");
}
{% endhighlight %}
