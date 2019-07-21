---
layout: post
title: Algebraic data types
date: 2019-07-17 18:45:20
img: functional-programming/x.png 
tags: [functional-programming, category-theory, oo-programming]
---

I also mentioned that functional programming is all about a very clear division between <b>data</b> and <b>operations</b>. We don't mix them. A class that has fields and methods is an instant smell. 

Remember how I mentioned before a couple of times when I think Scala I think algebra? What exactly is algebra if not objects (data) and operations on them (functions)? Today I will speak about the data chunk, so about "1" and "2" chunk from `1 + 2`.

### What are algebraic data types (ADTs)?

It's very important we get that straight because the concept is extremely simple yet definitions can get very confusing. Also, it sounds scary. 

<b>Algebraic data types</b> are all about data, and to be precise: data modelling. It's all about has-a, and is-a relationship. Good news is if you've used case classes before you've already worked with some kind of ADTs.

There is two types of ADTs: <b>sum</b> and <b>product</b>. Let's just into it.

### Sum 

A type with a number of possible instances. Think about sum as an <b> IS A </b> type.

For example types "Flavour" or "Creature". For either of them there is only a number of possible instances and they are the only ones allowed (like enum!). 

```java
sealed trait Flavour
case object Savoury extends Flavour
case object Salty extends Flavour
case object Sweet extends Flavour
case object Sour extends Flavour

sealed trait Creature
case object Human extends Creature
case object Alien extends Creature
case object Animal extends Creature
```
A String, an Int or anything else could not be a Flavour or a Creature.


#### Why sealed trait and case object and not just case classes?
In Scala sealed trait 



### Why "sum" and "product"? That's confusing.
I was confused about the naming for some time and there is a chance you are too (unless you're a mathematician). 

When you get confused about naming think of all possible options you could end up with.

For algebraic sum you have all of the options specified. If you <b>add</b> all of them up, you have all of your options.

For algebraic product there could be potentially infinite amount of options. Imagine your data type takes a Long. Your data then could be MyType(1), MyType(23), MyType(235554), MyType(0), MyType(4848444)... Now imagine your data type takes an Int, a Long and a String! You can remember "product" in a context of calculating all possible combinations.


