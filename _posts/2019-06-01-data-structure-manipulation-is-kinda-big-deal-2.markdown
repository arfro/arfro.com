---
layout: post
title: Data manipulation is kind of a big deal - part 2
date: 2019-06-01 13:32:20
img: transformations/factory.png 
tags: [transformation, aggregation, accumulation, fold, foldLeft, foldRight, reduce, functional-programming]
---

In this post I will continue on data manipulation by showing aggregation/accumulation methods - fold and reduce. There is a chance you've heard of "map and reduce" before in the context of big data - you know already what's `map` - now it's time to find out about `reduce`.
<br>

In a previous post I showed `map` and `flatMap` - two methods to apply a function to a given collection and get that collection returned. But what if we want to aggregate or accumulate something without necessarily transforming our collection? In other words, what if we want to, for instance, get a sum of all integers in a given collection? `fold` and `reduce` come in handy. Let's dive in.

## What's the difference between reduce and fold?
A simple answer is: `reduce` accumulates a single result and `fold` accumulates a single result using a start value. If we call `reduce` on a list we need to provide a function - that function will be evaluated for the first two elements and this result will be then used as a first argument in the next evaluation.
Calling `fold` is identical with the difference that we provide a start value which will be used on the first evaluation with the first element.

It's a bit complicated to explain with sentences so in practice it would look like this, note the comments that show how the evaluation happens:
 {% highlight scala %}
 val list = List(1, 2, 3, 4, 5)
 
 list.reduce(_ + _) // 15
 // 1 + 2 (first and second element)
 // 3 + 3 (result of above + third element)
 // 6 + 4 (result of above + fourth element)
 // 10 + 5 (result of above + fifth (last) element)
 // 15
 
 list.fold(0)(_ + _) // 15
 // 0 + 1 (start value + first element)
 // 1 + 2 (result of above + second element)
 // 3 + 3 (result of above + third element)
 // 6 + 4 (result of above + fourth element)
 // 10 + 5 (result of above + fifth (last) element)
 // 15
 
 list.fold(1)(_ + _) // 16
 // 1 + 1 (start value + first element)
 // 2 + 2 (result of above + second element)
 // 4 + 3 (result of above + third element)
 // 7 + 4 (result of above + fourth element)
 // 11 + 5 (result of above + fifth (last) element)
 // 16

 {% endhighlight %}


Obviously the function `_ + _` can be anything that is a valid function on two elements of a given collection, here: any function that is valid for two elements of type `Integer`. 

Also, just as a reminder, `_ + _` is a syntactic sugar for an anonymous function on a tuple `(a, b) => a + b`

## When to reduce and when to fold?
Know when to hold 'em, know when to fold 'em.

The first two questions I had when I saw `fold` next to `reduce` were:
1. if they do pretty much the same thing, what's the real difference between them - how do I know when to use which? 
2. why would I need a "start value"?

Let me try and shed some light on that.

To answer the first question in a simple term - use `reduce` when your result will be of same type as the type of elements in a collection. For example:
- get a sum of all elements in a `List[Int]` - the result will be `Int`
- get max of all elements in a `List[Int]` - the result will be `Int`

Use `fold` when your result will be of a different type than elements of your collection OR if there is a start value you would like to pass as the first element. For example:
- convert a list of integers to a string. (`reduce` wouldn't work here because it would be expecting the final result to be `Int`, not `String`, as mentioned above)
{% highlight scala %}
 val list = List(1, 2, 3, 4, 5)
list.fold("")(_.toString + _.toString) // "12345" (*)
list.fold("0")(_.toString + _.toString) // "012345"
{% endhighlight %}

As for the second question about the "start value" - as you can see above, in collections it would be used to tell the compiler what type we expect. If we changed above to:
{% highlight scala %}
 val list = List(1, 2, 3, 4, 5)
list.fold(0)(_.toString + _.toString) // start value is Int but elements in the function are String. Start value type dictates the return type
// error: type mismatch;
// found   : String
// required: Int

{% endhighlight %}
...the code wouldn't compile.


## foldLeft and foldRight
Without going into much detail there is two variations of `fold`: 

`foldLeft` which "traverses" your collection from left to right and is *tail recursive* and `foldRight` which "traverses" from right to left and is *head recursive*. (remember tail recursion vs head recursion? now you know that `foldRight` might blow up on a large collection!)

`foldLeft` in practice:

{% highlight scala %}
 val list = List(1, 2, 3, 4, 5)
list.foldLeft(0){ (a, b) => {
    println(s"a ($a) + b ($b)")
    a + b
}} 

// a (0) + b (1)
// a (1) + b (2)
// a (3) + b (3)
// a (6) + b (4)
// a (10) + b (5)
// res19: Int = 15
{% endhighlight %}

`foldRight` in practice:

{% highlight scala %}
 val list = List(1, 2, 3, 4, 5)
list.foldRight(0){ (a, b) => {
    println(s"a ($a) + b ($b)")
    a + b
}} 

// a (5) + b (0)
// a (4) + b (5)
// a (3) + b (9)
// a (2) + b (12)
// a (1) + b (14)
// res20: Int = 15
{% endhighlight %}

You can see the result is the same. Now take a moment to think about the difference in results if our function was not addition but subtraction - the results would surely differ! Also, knowing the difference between head and tail recursion can you see how it would make more sense to try and make the most of folding using `foldLeft`?


## Realistically, how often do you use those?
<b>As much as you can</b>, it's a quick a painless way to calculate accumulations and it can be used to solve some well known problems in a smart and quick way. Other than work on collections `fold` and `reduce` could be used as a form of error handling as it can replace pattern matching (more on error handling in a separate post):
{% highlight scala %}
Some(12).fold(0)(_ + 10) // 22
None.fold(0)(_ + 10) // 0
List().fold(0)(_ * _) // 0
{% endhighlight %}
In the examples above you can see that folding a `None` or an empty list results in the "start value" returned. 

You could also use `fold` to transform `Either` regardless of whether it's `Left` or `Right`. The way `fold` is implemented on `Either` is a shortcut to pattern matching:
{% highlight scala %}
val eitherRight: Either[String, Int] = Right(2)
val eitherLeft: Either[String, Int] = Left("problem")

eitherRight.fold(l => "left", r => r.toString) // "2"
eitherLeft.fold(l => "left", r => r.toString) // "left"
{% endhighlight %}
Think about above fold as "if this Either is Left then evaluate first function I passed but if this Either is Right evaluate the second function I passed".

Here is also a few ideas on how to solve some easy problems using what I explained today, compare them in your head to solutions that wouldn't use `fold` or `reduce`.

### get maximum from a list of integers
{% highlight scala %}
val list = List(1, 2, 3, 4, 8)
list.reduce(_ max _)
{% endhighlight %}

### get the shortest word in a sentence
{% highlight scala %}
val sentence = "Which word is the shortest?"
sentence.split(" ").reduce{
     (one, two) => 
        if (one.length < two.length) one 
        else two
     }  
 // "is"
{% endhighlight %}

### revert a list
{% highlight scala %}
val list = List("anna", "brad", "aga", "steve")
list.foldLeft(List[String]()){
      (one, two) => two :: one
     }
// List[String] = List(steve, aga, brad, anna)
{% endhighlight %}


## Final note
I can't stress enough how helpful it is to practice on little code tasks like on codewars.com or so to wrap one's head around `fold` and `reduce`. It's another way of thinking about problems - I can guarantee with time you will discover they are quite important concepts in functional programming. That being said, sometimes you will notice you can replace your `fold` or `reduce` with a simpler looking pattern match - then it's completely up to you. Myself, if my `fold` or `reduce` look overly complicated I would probably try to improve readability by choosing a simpler looking solution. 

____
(*) in this case "" is String's identity element for operation of concatenation.

When an identity element is paired with any element via the operation, it returns that element.

In practise: for addition of integers identity is 0 because 1 + 0 = 1. For multiplication it's 1 because 4 * 1 = 4. It's probably a questionable choice to speak of identity for Strings, but it works for our simple example
