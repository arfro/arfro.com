---
layout: post
title: Algebraic data types are all about... data
date: 2019-07-17 18:45:20
img: sumproduct/sumproduct.png 
tags: [functional-programming, adt, algebraic-data-types, sum, product]
---

I mentioned that functional programming is all about a very clear division between <b>data</b> and <b>operations</b>. We don't mix them. An OO like design where a class has private fields and methods that act on them is an instant smell. 

Remember how I mentioned before a couple of times when I think Scala I think algebra? What exactly is algebra if not objects (data) and operations on them (functions)*? Today I will speak about the data chunk, so precisely about "1", "2" and "3" in `1 + 2 = 3`.

## What is algebraic data type (ADTs)?
First things first - in the context of functional programming <b> ADT DOES NOT mean ABSTRACT DATA TYPE </b>. I heard this mistake a number of times and as you will realise after this post, these are two separate (and ironically pretty much opposite) concepts.

Ok. Let's get to it. It's very important we get ADTs straight because the idea behind them is extremely simple yet definitions can get very confusing. Also, it sounds like some crazy functional programming thing, almost like the M word... but trust me - it's not!

<b>Algebraic data types</b> are all about (and only about) describing data. It's all about has-a, and is-a relationship. Good news is if you've used case classes before you've already worked with some kind of ADTs.  

There is two basic types of ADTs: <b>sum</b> and <b>product</b>. Let's just dive into it.

### Sum ADT
A type with a limited amount of possible instances. Think about sum as an <b> IS A </b> or <b> OR </b> type.

For example type "Flavour". There is only a number of possible instances and they are the only ones allowed (like enum!). 


{% highlight scala %}
sealed trait Flavour
case object Savoury extends Flavour
case object Salty extends Flavour
case object Sweet extends Flavour
case object Sour extends Flavour
{% endhighlight %}

Think about the IS-A / OR part in terms of:
- Savoury <b>IS</b> a kind of Flavour.
- Long <b>IS NOT</b> a kind of Flavour.
- There <b>IS</b> four flavour options: Savoury, Salty, Sweet and Sour.
- Flavour can be Sour <b>OR</b> Sweet
- Flavour can't be Sweet <b>AND</b> and Sour at the same time (ok, that's controversial and maybe not the best modelling of real life food, but you get the idea!)


#### Why sealed trait and case object and not just case classes?
Two main reasons: we only need one instance of each value (why would we need two instances of Savoury?) AND we want to pattern match on them.

Sealed traits can only be extended in the file where they're defined. We wouldn't like to have someone rock up and extend our base trait (Flavour) in some random file. We want to look at our ADT and know exactly what data model we're working with, no hidden surprises.

As for case objects: objects in Scala are singletons and case classes are a dream for pattern match. One can then guess then that a case object takes a little from both worlds and ends up as a singleton that is easily pattern matched. (they are more than that though - do look them up!)

#### What should I model using Sum ADT?
In general, when you know all possible values. Kinda like enum. Here is some ideas:
- AccountStatus: Active, Inactive, Blocked
- Direction: North, South, East, West
- Season: Winter, Summer, Spring, Autumn
- Gender: Female, Male, Other
- Continent: Europe, Asia, Africa, NorthAmerica, SouthAmerica, Antarctica, AustraliaOceania
- WithdrawalError: NotEnoughFunds, AccountBlocked, FundsUnavailable

For the last case of errors do remember it's singletons, so only use Sum if you don't care about the error message. As a side note: notice how we wouldn't throw an exception but use ADTs instead. ADTs force you in a way to think of all your edge cases.

### Product ADT
A type that allows you to create concrete instances. Think about it as a <b>HAS A</b> or <b>AND</b> type.

{% highlight scala %}
sealed trait Creature
case class Human(name: String, dateOfBirth: Date) extends Creature
case class Alien(name: String, spaceshipId: SpaceshipId) extends Creature
case class Animal(name: String, dateOfBirth: Date) extends Creature
{% endhighlight %}

Obviously humans, aliens and animals have certain <b>traits</b> that separate them and make them different kinds of creatures, but let's ignore this now for simplicity.

Think about the HAS-A / AND part in terms of:
- Human <b>HAS A</b> name <b>AND</b> a dateOfBirth
- Alien <b>HAS A</b> name <b>AND</b> a spaceshipId

#### Why sealed trait and case classes?
Sealed trait for the same reason as sum - we want everything in one place. Case classes because we want to have Humans, Aliens and Animals instantiated with given constructor parameters and also to be able to pattern match on them.

#### What should I model using Product ADT?

- recursive structures:
{% highlight scala %}
sealed trait Person
case class Child(name: String, parent: Person) extends Person
case class Parent(name: String) extends Person
{% endhighlight %}

- errors with messages
{% highlight scala %}
sealed trait Error
case class RegistrationError(msg: String) extends Error
case class LoginError(msg: String) extends Error
{% endhighlight %}

- anything else that feels natural as a product really!

### Hybrid ADT
All that being said, in programming we don't always have a silver bullet and a ready solution. There might be cases when mixing Sum and Product might make sense. A great example from Alvin Alexander:
{% highlight scala %}
sealed trait Shape
final case class Circle(radius: Double) extends Shape
final case class Rectangle(width: Double, height: Double) extends Shape
{% endhighlight %}

### Why are they called "sum" and "product"? That's confusing.
I was confused about the naming for some time and there is a chance you are too. When you get confused about naming think <b>"what's a list all possible instances I could end up with?"</b>.

For algebraic <b>sum</b> you have all of the options (instances) specified. If you <b>add</b> all of them up, you have a list of all your instances. For `Creature` your only options are: `Human`, `Alien` and `Animal`

For algebraic <b>product</b> there could be potentially infinite amount of instances. Imagine your data type takes a Long as a constructor. Your data then could be MyType(1), MyType(23), MyType(235554), MyType(0), MyType(4848444)... Now imagine your data type takes an Int and a String! You'd have to "multiply" all possible Strings by all possible Ints to get all possible instances. So let's see... MyType(-2147483648, ""), MyType(-2147483647, "a"), MyType(-2147483646, "b")... you get the idea!

### But if ADTs are all about data then how do you do anything with them?
Algebra to help, again! 

If ADTs are "1", "2", "3" in `1 + 2 = 3` that means that they are only representation of data and require * something * to operate on them. Sounds like <b>functions</b>, no?

{% highlight scala %}
sealed trait Shape
final case class Circle(radius: Double) extends Shape
final case class Rectangle(width: Double, height: Double) extends Shape

def calculateShapeArea(shape: Shape) = {
    shape match {
        case Circle(r)       => circleArea(r)
        case Rectangle(w, h) => rectangleArea(w, h)
    }
}
{% endhighlight %}


A reminder of two rules of thumb I mentioned a couple of times already:
1. update as you copy, don't mutate!
2. write functions, no classes with fields and methods, remember - data and functions are separated, like in algebra

## Summary
ADTs are not more and not less than a way of representing the data. There is a sum (sealed trait with case objects) and a product (sealed trait with case classes). Sometimes it's ok to come up with a hybrid too!

__________

(*) If you're a mathematician you will have to say a lot about it, but let's keep it simple.

References:<br>
_Scala Cookbook_ by A. Alexander<br>
[Haskell Wiki on ADTs](https://wiki.haskell.org/Algebraic_data_type) 