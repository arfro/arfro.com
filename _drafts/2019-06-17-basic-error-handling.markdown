---
layout: post
title: Functional error handling in Scala
date: 2019-06-01 13:32:20
img: error-handling/error.png 
tags: [error-handling, option, try, either, referential-transparency, pure-function, functional-programming]
---


Let's talk about error handling. In functional programming we don't like side effects. We don't like cases we didn't think of before and error handling is exactly that - things that can go wrong and that can result in side effects. How to handle errors in Scala in a functional way?

I mentioned in the very first post that when I think of Scala / functional programming I think _algebra_. What would your maths teacher have to say about you solving your equation like this:

{% highlight scala %}
x = 3
y = 0
x / y = error
{% endhighlight %}

...or...

{% highlight scala %}
x = 3
x / y = null
{% endhighlight %}

If functional ways are like algebrae, then we can't allow <b>null</b> values or <b>exceptions</b>. Null values break _referential transparency_ rule, or in other words, they break the purity of a function. Exception do that to some extent. Let's clarify.

### What's referential transparency and a pure function?
<b>Pure function</b> is a function which:
* has its return value depend ONLY on its arguments:
{% highlight scala %}
def add(x: Int, y: Int) = x + y
{% endhighlight %}
* return value of this function will at all times without exception be the same
{% highlight scala %}
def add(x: Int, y: Int) = x + y
add(1, 4) // 5
add(1, 4) // 5 again, this could never evaluate to anything else
{% endhighlight %}
* the return value of this function can be used interchangeably with this function call
{% highlight scala %}
def add(x: Int, y: Int) = x + y
add(1, 5) == 6 // true at all times as add(1, 5) is the same as 6 at all times
{% endhighlight %}

<b>Referential transparency</b> refers to the last point - in simplification, it means that you can always replace a function call with it's return value.

### How nulls break referential transparency?
Think about it in very simple terms, if your function returns `null` and the another function 

### Why exceptions break referential transparency "to some extent"?
Exceptions should be used according to their name - exceptionally. When a situation is a true exception. Is division by zero a true exception? No. Is 
That leads to (perhaps a controversial) statement that exceptions break referential transparency only if they're explicitly observed and acted upon. 


### Real world referential transparency
Following referential transparency blindy would mean that we can't have any IO, data stores, random numbers and we can't even deal with time/date. What's the use of programming 
then?


### No nulls and no exceptions sounds sketchy
Perhaps for imperative ways. Scala does not have a notion of a checked exception like Java, where the compiler reminds you (to a degree) of error handling - so in Scala whatever you tell your function return, compiler assumes it will always return it.

### Nulls

### Exceptions


Depending on how purist you are again - sometimes I do throw excpetions in my code - but only in truly exceptional situations, e.g. registry of microservices: waiting for a microservice times out. Then I wrap it in Try and handle appropriately.

 
 


Rules of thumb: 
* Options instead of nulls. If you have a null anywhere in your Scala code that's a <b>very</b> serious smell - something I would reject a PR over or if already in the codebase I push to do a refactor ASAP.
* Try for exceptions (e.g. lib from Java)




