---
layout: post
title: Functional error handling in Scala
date: 2019-06-01 13:32:20
img: error-handling/error.png 
tags: [error-handling, option, try, either, referential-transparency, pure-function, functional-programming]
---


Let's talk about error handling. In functional programming we don't like side effects. We don't like cases we didn't think of before and errors are exactly that - things that can go wrong and that can result in side effects. How to handle errors in Scala in a functional way?

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

If functional ways are like algebrae, then we can't allow <b>null</b> values or <b>exceptions</b>. Null values break _referential transparency_ rule, or in other words, they break the purity of a function. Exception do that too (to some extent). Let's clarify.

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

<b>Referential transparency</b> refers to the last point - in simplification, it means that you can always replace a function call with its return value.

### No nulls sound sketchy
If you ever debugged `NullPointerException` you will appreciate the no null rule. If not, think that null is not a value - it represents an absence of a value. And such thing doesn't exist in algebra. In the context of Scala I only came across using null in a context of working with some Java libraries that _could_ return a null. Still then, my Scala code would not use null (I will talk about this a bit more below).

Scala does not have a notion of checked exceptions like Java, where the compiler reminds you (to a degree) of your error handling - so in Scala whatever you tell that your function will return, compiler assumes you aren't lying.

### Why exceptions break referential transparency "to some extent"?
Exceptions should be used according to their name - exceptionally. When a situation is a true exception. Is division by zero a true exception? No. If your recursive function unexpectedly blows up the stack, is it a true exception? Probably yes. (but that also means - shame on your tests!)

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
private def calculateAge(bday: Date): Int = {
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
case class Customer(name: String, age: Option[Int]) 
// age can be updated to Some later
{% endhighlight %}
* when you don't care about the error message
{% highlight scala %}
def divide(one: Int, two: Int): Option[Int] =
   two match {
    case t if t == 0 => None
    case t => Some(one/two)
   }
{% endhighlight %}

#### Summary
`Option` == possible absence of data OR we don't care about error message

### Either and its usage in a real world
`Either` is very similar to Option with a difference that it allows to retrieve an error message. It can be either `Left` or `Right` - `Left` suggests a problem (similar to `None` from `Option`), and `Right` suggest everything went ok (similar to `Some` for `Option`).

Let's have a look at the birthday example from above implemented using `Either`.
{% highlight scala %}
// only a type alias to make below Either more explicit and easier to understand
// This means nothing more than: type "Age" means type "Int"
type Age = Int 
// this will hold our error detail
case class CalculationProblem(problem: String) 

def calculateAge(birthday: Option[Date]): Either[CalculationProblem, Age] =
    birthday match {
        case Some(bday) => Right(calculateAge(bday))
        case None => Left(CalculationProblem("Birthday date missing")
    }
private def calculateAge(bday: Date): Int = {
    ...
    21 // everyone is 21 obviously ;)
 }
{% endhighlight %}
`Either` is used a lot, I would say about the same as `Option`. Of course there is a ton of much better ways to implement `calculateAge` - there is no doubt about it, but let's use it to grind out a few good ideas on how and when I use `Either` in real life.

* when you want to get the error message and act on it you package it in `Left`. `Right` will represent the happy path.
* Use `Either` instead of throwing exceptions - you can create a case class (or a whole hierarchy even, kinda like with exceptions) to hold your error message as I did above (that's a common practice too)
* make sure your `Either` is comprehensible and easy to reason about in a context it appears in. That means there is times when it's better to create types or aliases and have `Either[DatabaseConnectionProblem, WriteSuccess]` instead of `Either[Problem, Unit]` - think how easy or difficult it is to place first `Either` vs second `Either` in a context of what your code does.

#### Summary
`Either` == possible failure of operation AND we care about error message


### Try and its usage in a real world
Last but not least (although used the least from all three types I mentioned today) is `Try`.
You can think of `Try` as a specialized kind of `Either` where `Left` is not `Left` but `Throwable`. That means `Try` is a great option for working with code that throws exceptions (think Java libraries). The constructor automatically "catches" the exceptions should they be thrown and places them in `Try`s equivalent of `Left` - `Failure`. Happy path outcome goes into `Success`.

Using `Try` with our `calculateAge` would be a bit of an overkill, so let's have a look at the division again:
{% highlight scala %}
def divide(one: Int, two: Int): Try[Int] = Try(one/two)
divide(1, 0) // Failure(java.lang.ArithmeticException: / by zero)
divide(1, 1) // Success(1)
{% endhighlight %}
...think how easy it would be to pattern match to handle this and this way break out from bubbling the exception up:
{% highlight scala %}
divide(1, 0) match {
    case Success(result) => result
    case Failure(fail) => handleFailure
}
{% endhighlight %}

There is no silver bullet but generally `Try` is used a bit less than `Either` and `Option` for a simple reason that we generally don't expect to see exceptions in Scala code. `Try` is a great choice for:
* code that throws exceptions (Java libraries)

#### Summary
`Try` == code wrapped in `Try` could throw an exception

### Should I ever throw exceptions then?
As mentioned above - only in <b>truly</b> exceptional situations. I'm a purist when it comes to defining an exception (in sense of a throwable) - if you know it can happen in your code, it's not an exception, it's just an unhappy path. And as throwing an exception in Scala code is a big big deal for me I remember very clear there was <b>only one</b> time in the duration of my career where an explicit exception was allowed in the Scala codebase I worked on.

 
### Error handling summary
When you think about it, following referential transparency blindly would eventually lead us to think that we can't have any IO operations / data stores, not even random numbers or time/date operations. What's the use of programming then? 

As I mentioned a number of times in other posts already, it is all down to how much of a purist you want to be. In my code I follow those rules:

* I don't use nulls. Ever. I use `Option` if I don't care why something might be `None`, and `Either` if I want to be able to know the failure reason
* I don't throw exceptions. I use `Either` instead.
* I don't try/catch exceptions from code that could throw them (e.g. Java libraries) - instead I wrap the offending code in `Try` and pattern match on `Success` or `Failure` - that's where the exception stops bubbling up
* I think of exceptions and nulls as evil - I do whatever it takes not to use them, even if that means seeking help from my senior colleagues on alternatives I might not be aware of.
 
_____
References:<br>
_Scala Cookbook_ by A. Alexander <br>
_Programming in Scala: Simplified_ by A. Alexander <br>
https://www.garysieling.com/scaladoc/
