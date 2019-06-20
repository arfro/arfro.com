---
layout: post
title: For comprehensions
date: 2019-06-20 13:32:20
img: for-comprehensions/for.jpg
tags: [for-comprehension, flatMap, map, filter, yield, functional-programming]
---

Now that we know all about `map` and `flatMap` there is one more thing that's very important to know - <b>for comprehension</b>, which you can think of as a "kind of" replacement for a for loop - although you will soon see it isn't the same.

In short - a <b>for comprehension</b> gives us a syntactic sugar for working with large, sequenced and/or nested computations using `flatMap`, `map` and `filter`.

Without further ado let's first look at the below example. Let's say we have two lists of Integers and we want to get all combinations of multiplications on their elements, so for `List(1, 2)` and `List(3, 4)` we want a result of `List(3, 4, 6, 8)`

{% highlight scala %}
val list1 = List(1, 2)
val list2 = List(3, 4)

list1.flatMap(
    list1Element => list2.map(
        list2Element => list1Element * list2Element
    )
)

// List(3, 4, 6, 8)
{% endhighlight %}

...yikes. That doesn't look pretty. Thankfully, a <b>for comprehension</b> gives us a great syntactic sugar and makes it easy to rewrite the above as: 

{% highlight scala %}
val list1 = List(1, 2)
val list2 = List(3, 4)

for {
    l1Element <- list1 // for each list1 element called l1Element
    l2Element <- list2 // and for each list2 element called l2Element
  } yield l1Element * l2Element // multiply l1Element and l2Element

// List(3, 4, 6, 8)
{% endhighlight %}

That looks much more pleasant, right?

## Expressions allowed in for comprehensions

In general a for comprehension follows this syntax:

{% highlight scala %}
for {
      sequenceOfExpressions
    } yield computeResultExpression
{% endhighlight %}

Here is a list of allowed for comprehension expressions (`sequenceOfExpressions` in above pattern):
* generators (`element <- list`), think of it as "for each `element` of `list`"
* definitions (`name = element.name`), think of it as a simple assignment, just like `val dogName = dog.name`
* filters (`if (name == "Joe")`), well. Just a filter :)

`yield` is our door out from a for comprehension - this is the only place where your values and expression evaluations from the for comprehension are in scope, this is a place to act on what to do with your result.


## For comprehension rules

Now that we know the allowed expressions and what `yield` does let's talk about some rules. Let's start with an important note:

### Don't try to memorise those!
It will come natural (and logical) to you with time.

#### Rule #1:
For comprehension must <b>always</b> start with a generator.

{% highlight scala %}
val list1 = List(1, 2)
val list2 = List(3, 4)

for {
    l1Element = list1(1) // error: '<-' expected but '=' found
    l2Element <- list2
  } yield l1Element * l2Element

// List(3, 4, 6, 8)
{% endhighlight %}

#### Rule #2:
A for comprehension with one generator is a simple `map`:

{% highlight scala %}
for {
    element <- list
  } yield element * 2
{% endhighlight %}

...translates to...

{% highlight scala %}
list.map(_ * 2)
{% endhighlight %}

#### Rule #3:
A for comprehension with two generators is a `flatMap` followed by `map`.

{% highlight scala %}
for {
    element1 <- list1
    element2 <- list2
  } yield element1 * element2
{% endhighlight %}

...translates to...

{% highlight scala %}
list1.flatMap(
    element1 => list2.map(
        element2 => element1 * element2
    )
)
{% endhighlight %}


#### Rule #4:
A for comprehension with a generator and a filter is a `withFilter` followed by a `map`:

{% highlight scala %}
for {
    element <- list
    if(element > 3)
  } yield element + 3
{% endhighlight %}

...translates to...

{% highlight scala %}
list.withFilter(
    element => element > 1
  ).map(_ + 3)
{% endhighlight %}

#### Rule #5:
A for comprehension without a `yield` is a `forEach` (watch for side effects! not recommended)
{% highlight scala %}
for {
    element <- list
    if(element > 3)
  } println(element)
{% endhighlight %}

...translates to...

{% highlight scala %}
list.foreach(element => println(element))
{% endhighlight %}

## For comprehensions in real life

Scala <b>for comprehensions</b> become a life saver if you have tasks such as the one below. Given above rules just think what a flatmap-map-filter monster you'd create to compute the same without a for comprehension.

{% highlight scala %}
for {
    result <- operation // generator
    result2 <- operation(result) // generator
    result3 <- operation(result, result2) // generator
    if(result3 == 0) // filter
  } yield doSomething(result3) // yield
{% endhighlight %}

Let's see. I'm not going to attempt to build a non-for-comprehension solution but given our rules above we can guess that it would involve:
* three nested flatMap/maps for generators
* withFilter for filter
* map for yield

In real life for comprehension is a great solution for any case where you need sequencing and orchestrating a number of operations. If your solution is a single `map` or `flatMap` maybe it's not worth using a for comprehension. On the other hand if you're working with a few sequential operations and find yourself mapping, filtering and flatmapping across types like `Try`, `Option`, `Future` or any sequence of those types - that's when for comprehension will shine. 

Let's list a few usages:
* making several database requests that return `Try` 
* making several http requests that return `Future`
* computations across a number of sequences, e.g. across 4 lists
* bootstrapping the application - database, modules, http clients etc. (although you might think of using dependency injection instead!)


## Summary

For comprehension is a syntactic sugar for dealing with a nested `flatMap`, `map` and `filter` expressions. 


_______

### (not so) hardcore note
If you ever heard the word <b>monad</b> (and I am sure you did and thought "wtf" - but don't worry I did too) right here is the first place where I am going to mention it (but without going into details or explanation yet). In a <b>massive</b> oversimplification things that can be flatmapped over are monads (that means a simple `Option` is a monad too - how cool to know you worked with this (not so) scary stuff already!). 

So your (not so) hardcore addition to summary is:
 
 >For comprehensions are great for working with and composing monads.