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

The _Average Joe_ For-Loop
--
We have a method and we want to execute it a certain number of times. Say, 10 times. We can easily write a `for-loop` that iterates `10` times and it's all good.

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

This is perfectly fine and gets the job done. But, and that _but_ is the whole reason for this post, `for` loops often come with those pesky boundary conditions. You can easily make a mistake with the initialization and set it to `1`, or maybe you messed up the condition and set it to `i <= 10`. Something like that. So what if there's a way to eliminate the need to write those conditions and simply specify _how many times_ do you want the loop to iterate? Well, we're in luck.

Extension Methods to the Rescue!
--
We can write an extension method on `int`, where it will iterate the number of times we want. We pass an `Action()` to the method, so you can execute whatever the method you want.

{% highlight c# %}
public static class Ext
{
    public static void DoItXTimes(this int count, Action action)
    {
        for (int i = 0; i < count; i++)
        {
            action();
        }
    }
}
{% endhighlight %}

Then call it like this:

{% highlight c# %}
static void Main(string[] args)
{
    10.DoItXTimes(() => SayHello());
}
{% endhighlight %}

You can simply call the extension method on an `int` (either literal or a variable), so there's not much mistaking. 

Now that wasn't too bad, but what if we need access to the iterator. Say our `SayHello()` method looks like this:

{% highlight c# %}
public static void SayHello(int count)
{
    Console.WriteLine("{0} Hello!", count);
}
{% endhighlight %}

Then we can't use the above method, but don't worry, all is not lost.

For-Each is the Way!
--
Then we can make use of `foreach`.

{% highlight c# %}
foreach (var i in Enumerable.Range(0, 10))
{
    SayHello(i);
}
{% endhighlight %}

Look at the [Documentation](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.range?view=netframework-4.7.2){:target="_blank"} for the `Range()` method. First parameter is the `Starting Integer` and the second is the `Count`. So the above code sample would start at `0` and run `10` times.

{% highlight c# %}
foreach (var i in Enumerable.Range(3, 4))
{
    SayHello(i);
}
{% endhighlight %}

About would iterate `4` times, from `3` to `6`.

