---
layout: post
title: Composition over inheritance
date: 2019-07-17 18:45:20
img: functional-programming/x.png 
fig-caption: # Add figcaption (optional)
tags: [functional-programming, category-theory, oo-programming]
---

Let's talk about design. In one of my first posts I mentioned <b>composability</b> - now is time to dig a bit deeper. 


### Composition vs inheritance

When I first started my functional programming journey I heard a lot of "composition over inheritance". Composition, just like inheritance, is a way of thinking and designing your programs that allows you to implement polymorphism. 

One liner polymorphism recap: things (objects, functions) can take many forms (if a function takes Animal type and a Cat is an Animal that means our function takes Cat* )

I also mentioned functional programming is all about a clear division between <b>what</b> (data) and <b>how</b> (behaviour). 

### It's all maths, right?
                         
![function composition]({{site.baseurl}}/assets/img/functional-programming/category-theory-small.png)

{% highlight scala %}
val A = List(1, 11, 111)
def f: Int => String = _.toString 
def g: String => Int = _.length
def gof: Int => Int = g compose f // g and f composed

A.map(gof) // List(1, 2, 3)
{% endhighlight %}

_____
* there could be exceptions when working with generics so for now we're disregarding variance, invariance and contravariance. Just remember all this _something-variance_ stuff has to do with polymorphism for generics.