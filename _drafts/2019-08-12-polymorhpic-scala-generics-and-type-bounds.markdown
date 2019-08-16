---
layout: post
title: Polymorphic Scala - generics, type bounds and other (not so) scary words
date: 2019-07-17 18:45:20
img: functional-programming/x.png 
fig-caption: # Add figcaption (optional)
tags: [functional-programming, generics, types, type-bounds]
---

I want to start this blog post with admitting one thing I find very true: functional programming jargon can make it tough to understand even intuitive concepts. Today I will try my best to drill through the confusing world of generics, type bounds, parametric polymorphism, abstract types, generic types, type inference and all other things that well... sound all the same in the beginning. You will have to forgive me repeat myself a few times in this post. It's all for the greater good!

### In high overview... what are we even talking about?
Polymorphism! Nothing more, nothing less.

"Poly" - many, "morphism" - shape.

There is three types of polymorphisms (what?!) :
* subtype polymorphism - good ould OOP (Cat is a subtype of Animal)
* <b>parametric polymorphism - generics. That's the one we'll talk about today </b>
* ad hoc polymorphism - let's leave this one out for now. It's kinda like generics, but more flexible

You guessed it - Scala supports all of them!

#### But why is this such a big deal?


### Ok. What is what? 
As I mentioned, the jargon doesn't make it easy for us, mortal developers. But that's ok. We'll become immortal soon. Let's try to make sense out of some of those big words and see that what hides behind them is actually very intuitive stuff. Let's consider below piece of code. Think of a class `Zone` as a zone where only specific <b>type</b> of things are allowed. Maybe like a zone in a city where only specific type of buildings are allowed. Fun fact: As this blog is my travel break project I need to mention I got inspired by actually seeing this in so many places in Peru! You would think putting 20 pharmacies on one street (or, as I call it here, in one pharmacy "zone") is a bad idea and would make half of them go bankrupt, but Peruvians would disagree. So let's model that:

{% highlight scala %}
trait Building
trait School extends Building
trait Shop extends Building
trait Pharmacy extends Shop
trait Llama

class Zone[A](val anything: A) 

{% endhighlight %}


First things first. Naming. Given the definition of our `Zone` class above consider this:
* `Zone[A]` is called a <b>generic class</b>. A class that takes a type as its constructor parameter. A generic class needs a type to be instantiated because as its name suggests, it only operates on generic values, not on concrete types. So think about `List` - could we know what it means to add all elements of `List[A]`? What about `List[Int]`?
* `A` is called an <b>abstract type</b>. It kinda makes sense. It's "abstract", because we can't reason about it. Simply because we don't know what it actually could be. And as we can intuitively guess we will need to provide something concrete.
* our generic class `Zone` demonstrates the second type of polymorphism - <b>parametric polymorphism</b>, where we need to pass a <b>type parameter</b> to achieve polymorphism. Yay! Another fancy term!

So to summarize our `Zone[A]` could be a `generic class "Zone" that takes an abstract type A as an argument`. Sounds smart but it's too long. In real life while discussing it I would probably just call it a `generic class "Zone"`. Calling a class "generic" implies it it's a class that takes type(s) as argument(s), as I mentioned above. 


### Using generics (a.k.a parametric polymorphism)

As mentioned above, a generic class takes a type parameter (that's why it's called parametric polymorphism). That means that if we wanted to build a zone of pharmacies from the code above we would pass Pharmacy as a type to the generics class `Zone`:

{% highlight scala %}
  val pharmacyZone = new Zone[Pharmacy](new Pharmacy) 
{% endhighlight %}

Good. What if we want to have a zone of shops only?

{% highlight scala %}
  val shopZone = new Zone[Shop](new Pharmacy) 
  val shopZone2 = new Zone[Shop](new School) // will get an error here: School is not the same type as Shop 
{% endhighlight %}

### Type inference - playing guessing games with Scala compiler

Scala compiler is awesome and it can do a lot of thinking for you. It's time to talk talk about <b>type inference</b>. But first, let's see what does "to infer" mean in real life:
_to infer (verb): to conclude from evidence or by reasoning_

Type inference is then Scala compiler "concludes the type from evidence", a.k.a. guessing which type you meant.


Maybe you guessed already where this is going?
{% highlight scala %}
  val shopZone = new Zone(new Pharmacy) 
{% endhighlight %}

Notice that in the above code we're not passing the type. We still need it though! In this situation Scala compiler will look at the above code and figure that since the class signature was `Zone[A](val anything: A)` and it you then pass an instance of type `Pharmacy` you then must mean that type A equals to `Pharmacy`.

Please note that <b>type inference</b> is a general term that is not only to do with generics but also other objects (functions, values):
{% highlight scala %}
val name = "John" // value type inferred to be String
def add(one: Int, two: Int) = one + two // function return type inferred to be Int
{% endhighlight %}

So to summarize, type inference happens anytime you let the Scala compiler figure the type out. As cool as it is, I try to avoid it and explicitly state my types, especially when nested code is involved. The code is much more readable and easier to reason about, let alone I'm less likely to see the infamous `Product with Serialable` type breaking my code (for which one of the reasons could be that you let the compiler overthink what you meant).

### Scala type bounds

#### Upper type bound

#### Lower type bound

### Summary

Wow! What a chapter... Let's recap some of those big words:
* generic class - a class that needs a type parameter
* parametric polymorphism - basically... generic programming ("parametric" because types are passed as class/function parameters)
* type inference - when Scala compiler guesses what type you meant
* 



                         
![function composition]({{site.baseurl}}/assets/img/functional-programming/category-theory-small.png)


http://like-a-boss.net/2013/03/29/polymorphism-and-typeclasses-in-scala.html


______
