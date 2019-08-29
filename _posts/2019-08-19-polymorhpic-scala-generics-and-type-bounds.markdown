---
layout: post
title: Polymorphic Scala - type bounds
date: 2019-08-19 18:45:20
img: polymorphism/poly.jpeg 
fig-caption: # Add figcaption (optional)
tags: [functional-programming, polymorphism, generics, type-bounds]
---

I want to start this blog post with admitting one thing I find very true: functional programming mixed with generics jargon can make it tough to understand even intuitive concepts. Today I will try my best to drill through the confusing world of type bounds, parametric polymorphism, abstract types, generic types, type inference and all other things that sound... well... foreign at least. You will have to forgive me repeat myself a few times in this post. It's all for a greater good!

### In high overview... what are we even talking about?
Polymorphism! Nothing more, nothing less. Let's recap what it stands for:

> "Poly" - many, "morphism" - shape.

There is three types of polymorphisms (what?!):
* subtype polymorphism - good ould OOP (Cat is a subtype of Animal)
* <b>parametric polymorphism - generics. That's the one we'll talk about today </b>
* ad hoc polymorphism - let's leave this one out for now. It's kinda like generics, but more flexible

You guessed it - Scala supports all of them! So next time you're in an interview you can go ahead answer a question of "tell me about polymorphism" with "oh, but which kind of polymorphism would you like for me to talk about?". Pretty cool if you ask me!


### Ok. What is what? 
As I mentioned, the jargon doesn't make it easy for us, mortal developers. But that's ok. We'll become immortal soon. Let's try to make sense out of some of those big words and see that what hides behind them is actually very intuitive stuff. Let's consider below piece of code. Think of a class `Zone` as a zone where only specific <b>type</b> of things are allowed. Maybe like a zone in a city where only specific type of buildings are allowed. Fun fact: As this blog is my travelling career break project I need to mention I got inspired by actually seeing this in so many places in Peru! You would think putting 20 pharmacies on one street (or, as I call it here, in one pharmacy "zone") is a bad idea and would make half of them go bankrupt, but Peruvians would disagree. So let's model that:

{% highlight scala %}
trait Building
trait School extends Building
trait Shop extends Building
trait Pharmacy extends Shop
trait Llama

class Zone[A](val anything: A) 

{% endhighlight %}


First things first. Terms. Given the definition of our `Zone` class consider this crazy naming:
* `Zone[A]` is a <b>generic class</b>. A class that takes a type as its constructor parameter. A generic class needs a type to be instantiated because (as its name suggests) it only operates on generic values, not on concrete types. So think about `List` - could we know what does it mean to add all elements of `List[A]`? What about `List[Int]`? 
* `A` is an <b>abstract type</b>. It kinda makes sense when you think about it. It's "abstract", because we can't reason about it. Simply because we don't know what it actually could be. And as we can intuitively guess we will need to provide something concrete.
* our generic class `Zone` demonstrates the second type of polymorphism - <b>parametric polymorphism</b> which means that we need to pass a <b>type parameter</b> to achieve polymorphism. 

So to summarize our `Zone[A]` could be called a `generic class "Zone" that takes an abstract type A as an argument`. Sounds smart but... it's too long. In real life while discussing it I would probably just call it a `generic class "Zone"`. Calling a class "generic" implies it it's a class that takes type(s) as argument(s), as I mentioned above. 


### Using generics (a.k.a parametric polymorphism)

As mentioned above, a generic class takes a type parameter (reminder: that's why it's called parametric polymorphism). That means that if we wanted to build a zone of pharmacies from the code above we would pass `Pharmacy` as a type to the generic class `Zone`:

{% highlight scala %}
  val pharmacyZone = new Zone[Pharmacy](new Pharmacy) 
{% endhighlight %}

Good. What if we want to have a zone of shops only? 

{% highlight scala %}
  val shopZone = new Zone[Shop](new Pharmacy) 
  val shopZone2 = new Zone[Shop](new School) // will get an error here: School is not the same type as Shop 
{% endhighlight %}

### Type inference - playing guessing games with Scala compiler

On a note related to the above code let's talk about <b>type inference</b>. Scala compiler is awesome and it can do a lot of thinking for you... Scala compiler infers types. 

Let's see what does it mean "to infer" in real life:
> to infer (verb): to conclude from evidence or by reasoning

So rephrasing the above type inference is then when Scala compiler _"concludes the type from evidence"_, a.k.a. guessing which type you meant.


Maybe you already figured out where this is going?
{% highlight scala %}
  val shopZone = new Zone(new Pharmacy) 
{% endhighlight %}

Remember that `Zone` class needs a type parameter? Notice that in the above code we're not passing the type. We still need it though! In this situation Scala compiler will look at the above code and figure out that since the class signature was `Zone[A](val anything: A)` and you then pass an instance of type `Pharmacy` you then must mean that type A equals to `Pharmacy`.

Please note that <b>type inference</b> is a general term that is not only to do with generics but also other objects (functions, values):
{% highlight scala %}
val name = "John" // value type inferred to be String
def add(one: Int, two: Int) = one + two // function return type inferred to be Int
{% endhighlight %}

So to summarize, type inference happens anytime you let the Scala compiler figure the type out. As cool as it is, I try to avoid it and explicitly state my types, especially when nested code is involved. The code is much more readable and easier to reason about, let alone I'm less likely to see the infamous `Product with Serialable` type breaking my code (for which one of the reasons could be that you let the compiler overthink what you meant).

### Upper and lower type bounds - it's all about them healthy boundaries 
`Bounds` refer to boundaries you set for the type parameter of a class/method. If juice and coffee are liquids and potatos are solids, it could make sense to model a generic class `Glass[???]` to only take types that are `Liquids` (who would order a glass of potatoes?) You therefore set a <b>bound</b>(ary) for the type parameter of the class Glass and so end up with something like `Glass[Liquid]`. You might have heard of `upper type bounds` and `lower type bounds` - let's check them out then.

#### Upper type bound
Upper bound is when a parameter type passed to a class constructor has to be "less or equal" to your type boundary ("less" = subclass, "equal" = itself). So in relation to the above example "upper" means that our type `Liquid` is the "highest" allowed type. It's a boundary from the top - hence "upper bound". Knowing this now the Scala syntax "less or equal to" `<:` will also make a lot of sense:

{% highlight scala %}
class Glass[T <: Liquid] // our type T has to be "less or equal" to Liquid
trait Liquid
trait Solid
class Juice extends Liquid
class Coffee extends Liquid
class Potato extends Solid

new Glass[Juice] // ok!
new Glass[Coffee] // ok!
new Glass[Liquid] // ok!
new Glass[Any] // nop! "Any" is "higher" than Liquid 
new Glass[Potato] // hmm.. why? nop! compile error
{% endhighlight %}

note if you know Java: `T <: Liquid` is basically `<T extends Liquid>`


#### Lower bound
Lower bound is when a parameter type passed to a class constructor has to be "greater or equal" to your type boundary ("greater" = superclass, "equal" = itself). In the relation to the below code again "lower" means that `Liquid` is the "lowest" allowed type. It's a boundary from below - hence "lower bound". And again, syntax `>:` then a lot of sense:

{% highlight scala %}
class Glass[T >: Coffee] // our type T has to be "greater or equal" to Coffee
trait Liquid
trait Solid
class Juice extends Liquid
class Coffee extends Liquid
class Potato extends Solid

new Glass[Coffee] // ok!
new Glass[Liquid] // ok!
new Glass[Any] // ok!
new Glass[Juice] // nop! Juice is not "greater" than Coffee
new Glass[Potato] // again, nop!
{% endhighlight %}

### Summary

Bit of a brain fry this time around! Let's recap some of those big words:
* <b>generic class </b>- a class that needs a type parameter `class Something[A]`
* <b>type parameter</b> - a type that you pass as a parameter to a class/method 
* <b>parametric polymorphism</b> - basically... generic programming ("parametric" because types are passed as class/function parameters)
* <b>type inference</b> - when Scala compiler guesses what type you meant
* <b>type bound</b> - a type that is a restriction as to what types are allowed as a parameter
* <b>upper type bound</b> - anything "greater" (`T >: A`) than this type bound is NOT allowed
* <b>lower type bound</b> - anything "less" (`T <: A`) than this type is NOT allowed


As a closing note, I suggest you spend a few minutes studying the class hierarchy in Scala. It's crucial to know this when working with generics. Or as we can call it from now on, with parametric polymorphism.


                         
![scala object hierarchy]({{site.baseurl}}/assets/img/polymorphism/hierarchy.png)

______

References:
<br>
[Scala docs](https://docs.scala-lang.org/tour/variances.html) - official Scala documentation<br>
[Bartosz Witkowski's blog](http://like-a-boss.net/2013/03/29/polymorphism-and-typeclasses-in-scala.html) - great little blog on a few Scala things <br>
[Java9s YouTube channel](https://www.youtube.com/channel/UCGOoAhrv-mOe3dB2PeSVQGw) - a set of YouTube videos on Java 9 <br>
