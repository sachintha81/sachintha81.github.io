---
layout: post
title:  "Project.Init();"
date:   2018-08-17 00:45:41 -0700
categories: [general, c#, jekyll, stackoverflow]
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

I've been deliberating for a while where to host all my code snippets, and, in a broader sense, all-things-programming I want quick access to. I've been using <a href="https://gist.github.com/sachintha81/" target="_blank">GitHubGist</a> for a while now, which is fast and easy. The problem, though, is that it's a very limited code-snippet hosting service, and doesn't give you a good way to explain things, like you would in StackOverflow, with markdown and whatnot. Then I stumbled upon <a href="https://pages.github.com/" target="_blank">GitHubPages</a> and <a href="https://jekyllrb.com/" target="_blank">Jekyll</a>, and I will not look back. Well, at least not untill the proverbial _they_ come up with something better.

And I must tell the story of why I wanted to make the jump from Gist to this. I'm often active on SO, answering questions and stuff. One question I keep answering and people keep asking (other than the never ending `NullReferenceException` question) is how to pass data between two `Forms` or `Windows`. And after <a href="https://stackoverflow.com/q/51864861/302248/" target="_blank">I saw the same question for the umpteenth time</a>, I had enough. So the next post is going to be a detailed answer to that which I can simply point to hereafter.

So... let's get started.
