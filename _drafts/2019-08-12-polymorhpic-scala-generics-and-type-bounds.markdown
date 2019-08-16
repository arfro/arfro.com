---
layout: post
title: Polymorphic Scala - generics, type bounds and other (not so) scary words
date: 2019-07-17 18:45:20
img: functional-programming/x.png 
fig-caption: # Add figcaption (optional)
tags: [functional-programming, generics, types, type-bounds]
---

I want to start this blog post with admitting one thing I find very true: functional programming jargon can make it tough to understand even intuitive concepts. Today I will try my best to drill through the confusing world of generics, type bounds, parametric polymorphism, abstract types, generic types, type inference and all other things that well... sound all the same in the beginning.

### In high overview... what are we even talking about?
Polymorphism! Nothing more, nothing less.

"Poly" - many, "morphism" - shape.

There is three types of polymorphisms (what?!) :
* subtype polymorphism - good ould OOP (Cat is a subtype of Animal)
* <b>parametric polymorphism - generics. That's the one we'll talk about today </b>
* ad hoc polymorphism - let's leave this one out for now. It's kinda like generics, but more flexible

### Ok. What is what? 
As I mentioned, the jargon doesn't make it easy for us, mortal developers. But that's ok. We'll become immortal soon. Let's try to make sense out of some of those big words and see that what hides behind them is actually very intuitive stuff. Let's consider below piece of code. Think of a class `Zone` as a zone of a specific type of buildings. Fun fact: As this blog is my career break project I need to mention I got inspired by actually seeing this in so many places in Peru! You would think putting 20 pharmacies on one street (or, as I call it here, in one pharmacy "zone") is a bad idea and would make half of them go bankrupt, but Peruvians would disagree. So let's model that:

{% highlight scala %}
trait Building
trait School extends Building
class Shop extends Building
class Pharmacy extends Shop
class Llama

class Zone[A]{
    def knock(building: A) = knockingFunction
}
{% endhighlight %}


First things first. Naming. Given the definition of our `Zone` class above consider this:
* `Zone[A]` is called a <b>generic class</b>. A class that takes a type as its constructor parameter. A generic class needs a type to be instantiated because as its name suggests, it only operates on generic values, not on concrete types. So think about `List` - could we know what it means to add all elements of `List[A]`? What about `List[Int]`?
* `A` is called an <b>abstract type</b>. It kinda makes sense. It's "abstract", because we can't reason about it. Simply because we don't know what it actually could be. And as we can intuitively guess we will need to provide something concrete.
* our generic class `Zone` demonstrates the second type of polymorphism - <b>parametric polymorphism</b>, where we need to pass a <b>type parameter</b> to achieve polymorphism. Yay! Another fancy term!




So to summarize our `Zone[A]` could be a `generic class "Zone" that takes an abstract type A as an argument`. Sounds smart but it's too long. In real life while discussing it I would probably just call it a `generic class "Zone"`. Calling a class "generic" implies it it's a class that takes type(s) as argument(s), as I mentioned above. It's good to know the more advanced labels too though in case you want to try and read some more complex material.

### Summary

Wow! What a chapter... Let's recap:
* generic class - a class that needs a type parameter
* parametric polymorphism - basically... generic programming ("parametric" because types are passed as class/function parameters)
* 



                         
![function composition]({{site.baseurl}}/assets/img/functional-programming/category-theory-small.png)


http://like-a-boss.net/2013/03/29/polymorphism-and-typeclasses-in-scala.html


______
