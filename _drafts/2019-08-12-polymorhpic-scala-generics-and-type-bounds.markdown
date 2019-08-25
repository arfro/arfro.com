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

On a note related to the above code let's talk about <b>type inference</b>. Scala compiler is awesome and it can do a lot of thinking for you. But first, let's see what "to infer" means in real life:
> to infer (verb): to conclude from evidence or by reasoning

Type inference is then when Scala compiler _"concludes the type from evidence"_, a.k.a. guessing which type you meant.


Maybe you already figured out where this is going?
{% highlight scala %}
  val shopZone = new Zone(new Pharmacy) 
{% endhighlight %}

Remember that `Zone` class needs a type parameter? Notice that in the above code we're not passing the type. We still need it though! In this situation Scala compiler will look at the above code and figure that since the class signature was `Zone[A](val anything: A)` and you then pass an instance of type `Pharmacy` you then must mean that type A equals to `Pharmacy`.

Please note that <b>type inference</b> is a general term that is not only to do with generics but also other objects (functions, values):
{% highlight scala %}
val name = "John" // value type inferred to be String
def add(one: Int, two: Int) = one + two // function return type inferred to be Int
{% endhighlight %}

So to summarize, type inference happens anytime you let the Scala compiler figure the type out. As cool as it is, I try to avoid it and explicitly state my types, especially when nested code is involved. The code is much more readable and easier to reason about, let alone I'm less likely to see the infamous `Product with Serialable` type breaking my code (for which one of the reasons could be that you let the compiler overthink what you meant).

### Upper and lower type bounds - cause everyone needs healthy boundaries!
`Bounds` refer to boundaries you set for the type paramater of a class/method. If juice and coffee are liquids and potatos are solids, it could make sense to model a generic class `Glass[???]` to only take types that are `Liquids` (who would order a glass of potatoes?) You therefore set a <b>bound</b>(ary) for the type parameter of the class Glass and so end up with something like `Glass[Liquid]`. You might have heard of `upper type bounds` and `lower type bounds` - let's check them out then, they're not scary.

#### Upper bound
Upper bound is when a parameter type is "less or equal" to your type boundary. So in relation to the above example "upper" means that nothing of a "higher" type than `Liquid` can be passed as a parameter. Knowing this now the Scala syntax "less or equal to" `<:` will also make a lot of sense:

{% highlight scala %}
class Glass[T <: Liquid] // our type T has to be "less or equal" to Liquid
interface Liquid
interface Solid
Juice extends Liquid
Coffee extends Liquid
Potato extends Solid

new Glass[Juice] // ok!
new Glass[Coffee] // ok!
new Glass[Liquid] // ok!
new Glass[Potato] // hmm.. why? nop! compile error
{% endhighlight %}

note if you know Java: `T <: Liquid` is basically `<T extends Liquid>`


#### Lower bound


#### Multiple type bound

Bounded and unbounded parameters Scala bounds, upper type bound and lower type bound
bounded type - there is bounds to a type, so maybe T that extends Car, we give bounds to type parameter we pass to the class/function. In java <T extends Car>

when to use: glass only used for liquid. This is UPPER TYPE BOUND
glass[T extends Liquid] in scala Glass[T <: Liquid] T is less than Liquid
interface Liquid
Juice extends Liquid
Milk extends Liquid
Cardbord

Glass[Milk] OK
Glass[Cardborad] NOK

multiple bounded types: Glass[T extends Liquid & Fluid]
for above it will need to be something thats a liquid and a fluid too

unbounded type - just T 

LOWER TYPE BOUND Glass[B >: A]


### Summary

Wow! What a chapter... Let's recap some of those big words:
* generic class - a class that needs a type parameter
* parametric polymorphism - basically... generic programming ("parametric" because types are passed as class/function parameters)
* type inference - when Scala compiler guesses what type you meant
* type bound - when your type is a bound e.g. T extends Juice 





                         
![function composition]({{site.baseurl}}/assets/img/functional-programming/category-theory-small.png)


https://docs.scala-lang.org/tour/variances.html

http://like-a-boss.net/2013/03/29/polymorphism-and-typeclasses-in-scala.html


______
