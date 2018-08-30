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

#### Here's the first attempt:  
{% highlight c# %}
// Do Process using nested if statements
void DoProcess1()
{
    LOG("DoProcess Started...");
 
    if (Step1() == true)
        if (Step2() == true)
            if (Step3() == true)
                if (Step4() == true)
                    if (Step5() == true)
                        if (Step6() == true)
                            Step7();
 
    LOG("DoProcess Finished");
}
{% endhighlight %}
