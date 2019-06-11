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

If functional ways are like algebrae, then we can't allow <b>null</b> values or (to some extent) <b>exceptions</b>. Null values break _referential transparency_ rule, or in other words, they break the purity of a function. Exception do that too (to some extent). Let's clarify.

### What's referential transparency and a pure function?
<b>Pure function</b> is a function which:
* its return value is not `Unit` - think about it: `Unit` strongly suggest we're on to something impure (like `println` perhaphs?)
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

### No nulls sound sketchy
If you ever debugged `NullPointerException` you will appreciate the no null rule. If not, think that null represents an absence of a value. And such this doesn't exist in algebra. In the context of Scala I only came across with using null in a context of working with some Java libraries that _could_ return a null. Still then, my Scala code would not use null (will talk about handling those cases below).

Scala does not have a notion of a checked exceptions like Java, where the compiler reminds you (to a degree) of your error handling - so in Scala whatever you tell your function will return, compiler assumes it will always return it.

### Why exceptions break referential transparency "to some extent"?
Exceptions should be used according to their name - exceptionally. When a situation is a true exception. Is division by zero a true exception? No. If your recursive function blows up the stack, is it a true exception? Yes. 

That leads to (perhaps a controversial) statement that exceptions break referential transparency only if they're explicitly observed and acted upon. That means - if you write code that throws or catches exceptions explicitly, you break referential transparency. If the code does it "on its own" - I personally think it's fine. The exception will be thrown in a truly exceptional situation then.


### Option and its usage in a real world
`Option` suggest an optional value - a value that might, or might not exist. If it exists it's wrapped in `Some`, if it doesn't - it becomes `None`. This goes very well with pattern matching.
 
 Let's have a look at basic usage below - imagine optional fields in registration, like "birthday". `calculateAge` function takes a date of birth and calculates the age.
{% highlight scala %}
def calculateAge(birthday: Option[Date]): Option[Int] =
    birthday match {
        case Some(bday) => Some(calculateAge(bday))
        case None => None
    }
def calculateAge(bday: Date): Int = {
    ...
    21 // everyone is 21 obviously ;)
 }
{% endhighlight %}
If our customer added their birth date their age will be calculated and returned wrapped in `Some`. If customer is missing their date of birth the `calculateAge` won't even be called and their age will be returned as `None`. It's up to the caller now to deal with it.

Option is used <b>a lot</b>. Some practical examples:
* optional arguments in functions:
{% highlight scala %}
def optionalStrings(str: Option[String]) = ...
{% endhighlight %}
* arguments that are not present on initialize
{% highlight scala %}
case class(name: String, ordersAmount: Option[Int]) // ordersAmount can be updated to Some later
{% endhighlight %}
* when you don't care about the error message
{% highlight scala %}
def divide(one: Int, two: Int): Option[Int] =
   two match {
    case t if t == 0 => None
    case t => Some(one/two)
   }
{% endhighlight %}

### Either and its usage in a real world

### Try and its usage in a real world


### What to do instead of nulls?
If you feel like you want to return a null it sounds like a code smell. Let's check some examples.

{% highlight scala %}

{% endhighlight %}

### What to do instead of throwing exceptions?

 
### Real world referential transparency
When you think about it, following referential transparency blindly would eventually lead us to think that we can't have any IO operations / data stores, not even random numbers or time/date operations. What's the use of programming then? 

As I mentioned a number of times in other posts already, it is all down to how much of a purist you want to be. In my code I follow those rules:

* I don't use nulls. Ever. I use `Option` if I don't care why something might be `None` and `Either` if I want to be able to know the failure reason
* 99.99% of the times I don't throw exceptions. I generally use `Either`
* I don't catch exceptions from code that could throw them (e.g. Java libraries) - I wrap the offender in `Try` and pattern match on `Success` or `Failure`
 


Rules of thumb: 
* Options instead of nulls. If you have a null anywhere in your Scala code that's a <b>very</b> serious smell - something I would reject a PR over or if already in the codebase I push to do a refactor ASAP.
* Try for exceptions (e.g. lib from Java)


____
*I will skip monads for now - just trust me that's one way to handle IO 

