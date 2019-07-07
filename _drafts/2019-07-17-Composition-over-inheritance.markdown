---
layout: post
title: Composition over inheritance
date: 2019-07-17 18:45:20
img: functional-programming/x.png 
fig-caption: # Add figcaption (optional)
tags: [functional-programming, category-theory, oo-programming]
---

### It's all maths, right?
                         
![function composition]({{site.baseurl}}/assets/img/functional-programming/category-theory-small.png)

{% highlight scala %}
val A = List(1, 11, 111)
def f: Int => String = _.toString 
def g: String => Int = _.length
def gof: Int => Int = g compose f // g and f composed

A.map(gof) // List(1, 2, 3)
{% endhighlight %}
