---
layout: post
title: How to structure functional code
date: 2019-09-17 18:45:20
img: structuring/modules.jpeg 
fig-caption: # Add figcaption (optional)
tags: [functional-programming, mix-in, traits, services, domain-driven-design]
---

Let's talk about design. In one of my first posts I mentioned <b>composability</b> - now is time to dig a bit deeper. It's not only functions that compose!


### Composition vs inheritance

When I first started my functional programming journey I heard a lot of "composition over inheritance". Composition, just like inheritance, is a way of thinking and designing your programs that allows you to implement polymorphism. (check my previous post for polymorphism recap)

I also mentioned functional programming is all about a clear division between <b>what</b> (data) and <b>how</b> (behaviour/operations). 

If you're anything like me back in the day you might be thinking now - ok, I know how to compose functions and I know why but... how do I take it to the next step? How do I actually <b>structure</b> my code? Those gorgeous composed functions must be a part of something <b>greater</b>.

### Modules, baby!

If there's no mutability and behaviours don't mix with data it's easy enough to compose that behaviour. Now, as we speak of how to organize your code below example is going to be a little lengthy. It shows how to use traits and  
get the effect of really cool <b>pluggable modules</b>. The idea is basically to have a bare class and add onto it until we have all behaviours that we want. This removes the need for the classic inheritance polymorphism and gives you a powerful tool to mix and match. In the above example, notice how I won't have a problem with inheritance when an unexpected requirement for `BathroomBakery` comes in - in classic inheritance I'd be cornered as `BathroomBakery` is not a "full" bathroom but also not a "full" Kitchen.

{% highlight scala %}
trait Room {
  def openTheDoor = "opened the door"
  def closeTheDoor = "closed the door"
}

trait LockableRoom extends Room {
  def lockTheDoor = "locked the door"
  def unlockTheDoor = "unlocked the door"
}

trait ToiletBowl {
  def useToiletBowl = "using toilet bowl"
}

trait Sink {
  def washHands() = "washing hands"
  def doTheDishes() = "done the dishes"
}

trait Shower {
  def takeAShower() = "taking a shower"
}

trait Fridge {
  def getCookingItems() = "getting all items for a pizza"
}

trait Oven {
  def cookAPizza() = "cooked a pizza"
}


trait BathroomWithShower extends LockableRoom with Sink with Shower with ToiletBowl
trait BathroomWithoutShower extends LockableRoom with ToiletBowl with Sink

trait Kitchen extends Room with Sink with Fridge with Oven

val classicKitchen = new Kitchen {}
val classicBathroom = new BathroomWithoutShower {} // create a new object and attach a trait to it


classicKitchen.openTheDoor // because kitchen is a Room
classicKitchen.washHands() // because kitchen has a sink
classicKitchen.getCookingItems() // because kitchen has a fridge
classicKitchen.cookAPizza() // because kitchen has an oven
classicKitchen.doTheDishes() // because kitchen has a sink
classicKitchen.closeTheDoor // because kitchen is a Room

classicBathroom.openTheDoor // because bathroom is a Room
classicBathroom.lockTheDoor // because bathroom is a LockableRoom
classicBathroom.useToiletBowl // because bathroom has a toilet bowl
classicBathroom.washHands() // because bathroom has a sink
classicBathroom.unlockTheDoor // because bathroom is a LockableRoom

// because requirements sometimes are weird and now we need a bathroom where there is no shower but there is an oven:
trait BathroomBakery extends BathroomWithoutShower with Oven
val weirdBathroom = new BathroomBakery {}

weirdBathroom.openTheDoor // because weird bathroom is a Room
weirdBathroom.cookAPizza() // because weird bathroom has an oven
weirdBathroom.useToiletBowl // because weird bathroom has a toilet bowl
weirdBathroom.closeTheDoor // because weird bathroom is a Room


trait BathroomModule {
  val bathroomWithoutShower = new BathroomWithoutShower {} 
  val bathroomWithShower = new BathroomWithShower {}
}

trait KitchenModule {
  val kitchen = new Kitchen {}
}
class HouseModule extends BathroomModule with KitchenModule // only mix modules with modules!!!

val house = new HouseModule
house.kitchen.getCookingItems()

{% endhighlight %}

### But functional programming is the opposite of object oriented so why like this?

<b>!!! Object oriented programming is NOT opposite to functional programming !!!</b> 

What had me wonder for a while in the beginning of my journey was that functional programming is so complex and that it is somehow completely opposite to object oriented programming and whatever we do in object oriented programming is the "less smart" way, evil and should not be done in functional. So NO polymorphism and NO instantiating ANYTHING and functions somehow float in the runtime and do their thing without working on any objects (as silly as it sounds - welcome in the head of an overthinker!). So again, the standard functional programming journey question followed - how do I even code then? 

Let's clear that confusion once and for all. <b>Functional programming is as opposite to object oriented just as taking a train is opposite to taking a bus.</b> It's not at all opposite. It's different yet serves the same purpose. You get to your destination, it might be more efficient or less efficient, it might be cheaper or more expensive, faster or slower, there can be a fabulous service or no service, it might take a road you know well or bounce off to go along meadows and mountains but it gets you to where you have to be at the end of the day. 

### What if a module depends on another module? In other words - do I do dependency injection and how do I do it?

Unless you hardcode the everloving life out of your application there is a chance that your service is useless without repository and so is your repository without config. If your app is trivial and only needs a handful of instances DI might be an overkill. In any other case it is quite beneficial. (I will skip the reasoning here but if unclear, do look it up)

There is a couple of techniques you can use for dependency injection. I will have a separate blog post on them. For now just trust me that the main and most popular ones are:
- self types
- reader monad
- third party tool like MacWire

Let's leave the rooms and toilet bakeries and move on to something more real life like. Dependency injection is crucial in module design. Let's see: 


{% highlight scala %}

case class Config(databaseName: String)

class DatabaseService(config: Config) {
  def saveAccount(s: String) = s"saving '$s' to database with name: ${config.databaseName}"
}

trait ConfigModule {
  val config = Config("db1") // imagine this is parsed from application.yaml config file
}

trait DatabaseModule { 
  this: ConfigModule => // self type dependency injection
    def dbService = new DatabaseService(this.config) // only mix modules with modules!
}


object App extends DatabaseModule with ConfigModule { // only mix modules with modules!
    // voila! I can use saveAccount and have access to config too!
}

{% endhighlight %}

Note on the above code: it's not perfect from the design point of view. It's just to demonstrate the idea of modules in a simple way without cornering myself with too much irrelevant code. `DatabaseService` is actually a repository that should be abstracted much more but let's leave this aside.

Explanation of the code (do read comments in the code for more clarity!):

* For any application to work we first we need an application.config and some config reader (config reader is skipped in the example here for simplicity)
* Then we need a config ca
Unless your app is trivial and only needs a handful of instances dependency injection is a good idea.se class to represent it as data
* Then we need a configModule to expose config
* Then we need a databaseService that takes config as arg, we can use it because it's exposed in ConfigModule
* Then we need a databaseModule to expose dbService

### What's the actual difference between composition and inheritance?

My rule of thumb is: <b>if it's a IS-A relationship use inheritance. If it's a HAS-A use composition.</b>

In functional approach we separate data from functions. 


is a = inheritance
inheritance is one way, use only if its a true is-a NOT for code reuse


has a = composition
use if its not an is-a but you want polymorphism

traits are cool - combine modules , adding markup interfaces, 


### What if I have some functions that don't belong to any of the modules/services?

Maybe you have a function such as `convertXtoY` or `extractX` that don't really belong to any module/service. You could then use `Utilities` object and just import whatever is needed whenever you need it. Always keep your modules and service scope clean. Ask yourself questions such as "does updating a list of borrowed books relate to `Account`?". If the answer is "yeah, definitely" then `updateBorrowedBooks` belongs to `AccountService`. If the answer is unclear or "no" then maybe it's a sign it belongs to another service, or perhaps a utilities object altogether.


### Problems with modules

There is no silver bullet and there is a couple of problems with the modular approach. It's all about being reasonable in picking your approach in design. I leave this as open questions to ponder more than trying to give a ready answer. I will probably pop in to update those with time and experience :) 

- what if we need to mix in two modules/services with the same value/function name? Scala compiler will overshadow earlier occurring one and only using the last occurring one.

- what if there is a 100 traits we need to extend? That's a very bad code readability and a high chance of naming clashes

### Recap

Let's recap:
- always keep your models (data) separate from your services (functions)
- structure your code into modules which are implemented as traits
- do dependency injection (post on that coming soon)
- only mix modules with modules
- if you have some functions that don't belong into any modules they could be a good candidate for a `Utility` object


![function composition]({{site.baseurl}}/assets/img/functional-programming/category-theory-small.png)

_____
* there could be exceptions when working with generics so for now we're disregarding variance, invariance and contravariance. Just remember all this _something-variance_ stuff has to do with polymorphism for generics.


functional design book by ghosh
softwaremill blogs