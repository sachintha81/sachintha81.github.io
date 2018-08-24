---
layout: page
title: Markdown
permalink: /markdown_syntax/
---

This really is a note-to-self demo. I'm using code-snippet markers to show the actual `Markdown Syntax` used for each item.

[View the markdown used to create this post](https://raw.githubusercontent.com/barryclark/www.jekyllnow.com/gh-pages/_posts/2014-6-19-Markdown-Style-Guide.md).

This is a paragraph, it's surrounded by whitespace. Next up are some headers, they're heavily influenced by GitHub's markdown style.
<br>
  
```
## Header 2 (H1 is reserved for post titles)##
```
## Header 2 (H1 is reserved for post titles)##
<br>
```
### Header 3
```
### Header 3
<br>
```
#### Header 4
```
#### Header 4
<br>
  
```
A link to [Jekyll Now](http://github.com/barryclark/jekyll-now/). 
```
A link to [Jekyll Now](http://github.com/barryclark/jekyll-now/). 
<br>
  
```
A big ass literal link <http://github.com/barryclark/jekyll-now/>
```
A big ass literal link <http://github.com/barryclark/jekyll-now/>
<br>
  
An image, located within /assets/images

```
![an image alt text]({{ site.baseurl }}/assets/images/jekyll.svg "an image title")
```
![an image alt text]({{ site.baseurl }}/assets/images/jekyll.svg "an image title")
<br>
  
```
* A bulletted list
- alternative syntax 1
+ alternative syntax 2
  - an indented list item
```
* A bulletted list
- alternative syntax 1
+ alternative syntax 2
  - an indented list item
<br>
  
```
1. An
2. ordered
3. list
```
1. An
2. ordered
3. list
<br>
  
Inline markup styles: 

```
- _italics_
- **bold**
- `code()` 
```
- _italics_
- **bold**
- `code()` 
<br>
  
```
> Blockquote
>> Nested Blockquote 
```
> Blockquote
>> Nested Blockquote 
<br>
  
Syntax highlighting can be used by wrapping your code in a liquid tag like so:

![Code Block]({{ site.baseurl }}/assets/images/codeblock.PNG "Code Block")

creates...

{% highlight c# %}
// Comment
public static void SomeMethod(string greeting)
{
    Console.WriteLine("Hello, {0}", greeting);
}
{% endhighlight %}
<br>
  
Use two trailing spaces  
on the right  
to create linebreak tags  
or use ```<br>```
<br>
  
Finally, horizontal lines
 
```
----
****
```
----
  
  
****

