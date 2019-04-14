---
layout: post
title: Data transformation is kind of a big deal - part 1
date: 2019-03-05 13:32:20
img: transformations/factory.png 
tags: [aggregation, transformation, filtering, functional-programming, map, fold, reduce, flatMap]
---
Data structure manipulation is a kind of a big deal. Now that I shared that recursion is important but there is other ways to achieve your goal let's look into `map`, `flatMap`, `filter`, `reduce` and `fold`. This post will focus on <b>collections</b> only - there is other important usages of those functions but let's focus on simple stuff first.
<br>

When I think Scala I like to think of designing a blueprint of a factory line. You can think of each of the above mentioned transformation functions as one station in a factory line. This sounds complicated but I promise the only thing it is is literally - do A, then B, then C.
<br>

Keeping that in mind let's recall Scala collections real quick:
* List
* Map
* Set
* tuple
* Option (a collection that's either empty or has one item)

### map
You can call `map` on all the collections. It applies a passed function to each element and returns a collection of new elements. <br>

{% highlight scala %}
def divideByTwo(number: Int) = number / 2

List(10, 20, 30).map(divideByTwo) // List(5, 10, 15)
{% endhighlight %}
... the function can also be anonymous, you will <b>very</b> often see code like:
{% highlight scala %}
List(10, 20, 30).map(nr => nr / 2) // List(5, 10, 15)
{% endhighlight %}

Of course the function used in `map` can be a function that goes from (in our case) `Int` to any other type - imagination is your limit. So let's maybe try a function that goes from `Int` to `String`?
{% highlight scala %}
List(10, 20, 30).map(nr => nr.toString) // List("5", "10", "15") 
{% endhighlight %}

<b>Summary</b><br>
You can think of `map` as "apply this function to each element of this collection"

### flatMap



* map
* flatMap 
* filter
* fold:
    - foldLeft
    - foldRight
* reduce:
    - reduceLeft
    - reduceRight
* collect
* forEach

How to think im map etc instead of loops?

Realistically how often do you use those?

Real life scenarios:
* map - on Option, will only execute if Some() - e.g
* flatMap - if you want to collect from a list of Option, will discard None
* filter - 
* fold:
    - foldLeft
    - foldRight
* reduce:
    - reduceLeft
    - reduceRight
* collect
* forEach
* mapConcat
* mapAsync



should mix and match and think in a new way of solving problems. Loops not allowed
Examples: <br>
* in List(1,3,4,5,6,7,8) list all odd numbers
* revert a string
* is palindrome
* https://www.geeksforgeeks.org/top-10-algorithms-in-interview-questions/ <- get like maybe 4 classic problems

----
References:<br>
_Functional programming in Scala: Simplified_ by A. Alexander<br>
_Scala Cookbook_ by A. Alexander