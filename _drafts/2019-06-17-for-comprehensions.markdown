---
layout: post
title: For comprehensions
date: 2019-05-01 13:32:20
img: for-comprehensions/forcomp.png
tags: [for-comprehension, flatMap, map, filter, yield, functional-programming]
---

Now that we know all about `map` and `flatMap` there is one more thing that's very important to know - <b>for comprehension</b>, which you can think of as a "kind of" replacement for a for loop - although you will soon see it isn't the same.

In short - a <b>for comprehension</b> gives us a nice to look at syntactic sugar for working with large and/or nested computations using `flatMap`, `map` and `filter`.

Without further ado let's first look at this beauty. Let's say we have two lists of Integers and we want to get all combinations of multiplications on their elements, so for `List(1, 2)` and `List(3, 4)` we want a result of `List(3, 4, 6, 8)`

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

## For comprehension allowed expressions

In general a for comprehension follows this syntax:

{% highlight scala %}
for {
      sequenceOfExpressions
    } yield computeResultExpression
{% endhighlight %}

Here is a list of allowed for comprehention expressions (`sequenceOfExpressions` in above pattern):
* generators (`element <- list`), think of it as "for each `element` of `list`"
* definitions (`name = element.name`), think of it as a simple assignment, just like `val dogName = dog.name`
* filters (`if (name == "Joe")`), well. Just a filter :)

`yield` is our door out from a for comprehension - this is the only place where your values and expression evaluations from the for comprehension are in scope, this is a place to act on what to do with your result.


## For comprehension rules

Now that we know the possible expressions let's talk about rules.

### You do not need to memorise/learn those to use for comprehension!
It will come natural to you with time.

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
A for comprehension with two generators:

{% highlight scala %}
for {
    l1Element <- list1
    l2Element <- list2
  } yield l1Element * l2Element
{% endhighlight %}

...is a `flatMap` followed by a `map`:

{% highlight scala %}
list1.flatMap(
    list1Element => list2.map(
        list2Element => list1Element * list2Element
    )
)
{% endhighlight %}


#### Rule #3:
A for comprehension with a generator and a filter:

{% highlight scala %}
for {
    l1Element <- list1
    if(l1Element > 3)
  } yield l1Element + 3
{% endhighlight %}

...is a `withFilter` followed by a `map`:

{% highlight scala %}
list1.withFilter(
    list1Element => if(list1Element > 3)
).map(_ + 3)
{% endhighlight %}

#### Rule #4:



## For comprehensions in real life

Scala <b>for comprehensions</b> become a life saver if you have tasks such as the one below. Given above rules just think what a flatmap-map-filter monster you'd create to compute the same without a for comprehension.

{% highlight scala %}
case class Human(name: String)
case class Dog(name: String)
val listOfHumans = List(Human("Anna"), Human("Aga"), Human("Max"), Human("Lisa"))
val listOfDogs = List(Dog("Coco"), Dog("Max"), Dog("Ruby"))

for {
    human <- listOfHumans // generator
    dog <- listOfDogs // generator
    humanName = human.name // definition
    dogName = dog.name // definition
    if(dogName == humanName) // filter
  } yield dogName // yield

// List(Max)
{% endhighlight %}

