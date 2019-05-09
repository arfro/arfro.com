---
layout: post
title: Data transformation is kind of a big deal - part 1
date: 2019-03-05 13:32:20
img: transformations/factory.png 
tags: [transformation, map, flatMap, functional-programming]
---
Data manipulation and transformation is a kind of a big deal. Now that I shared that recursion is important but also mentioned there is other ways to achieve certain goals let's look into that. This will be a first post in a mini series on data manipulation and transformation. I will focus on <b>collections</b> mainly - there is other important usages but I will focus on most intuitive stuff first.
<br>

Keeping that in mind let's recall Scala collections real quick:
* List
* Map
* Set
* tuple
* Option (a collection that's either empty or has one item, fabulous into from Alvin Alexander [here](https://alvinalexander.com/scala/using-scala-option-some-none-idiom-function-java-null))

When I think Scala I like to think of designing a blueprint of a factory line.
 
 


 Let's imagine you have a factory that fixes bikes. What could be our work flow? Maybe something like: 
* inspect the bike (get list of problems)
* fix all problems (if any)
* clean bike

We want to make sure that when the bike arrives, it passes through all of those stages. The bike gets transformed in a way - if it's broken it gets fixed and cleaned, but if it's not broken, it only gets cleaned.
<br>

With this image in mind, let's start with `map` and `flatMap`.<br>

### map
You can call `map` on all the collections. It applies a passed function to each element and returns a collection of new elements. (kinda like a stage in a factory)<br>

{% highlight scala %}
def divideByTwo(number: Int) = number / 2

List(10, 20, 30).map(divideByTwo) // List(5, 10, 15)
{% endhighlight %}
... the function can also be anonymous, you will <b>very</b> often see code like:
{% highlight scala %}
List(10, 20, 30).map(nr => nr / 2) // List(5, 10, 15)
{% endhighlight %}

Of course the function used in `map` can be a function that goes from (in our case) `Int` to any other type - sky is your limit. So let's maybe try a function that goes from `Int` to `String`?
{% highlight scala %}
List(10, 20, 30).map(nr => nr.toString) // List("5", "10", "15") 
{% endhighlight %}

<b>Summary</b><br>
You can think of `map` as "iterate over this collection and apply this function to each element". Does it not resemble a for loop does?

### flatMap



* map
* flatMap 


### Realistically, how often do you use those instead of loops etc?
<b>Every single day</b>, without a failure. I think `map` and `flatMap` are the first things you learn. I remember my very first Scala code, and by very first I literally mean first lines. I had this gorgeous for loop ready to go and someone pointed out in my code review that my 9 lines of code could be replaced with less than 10 characters... Of course, I was sold!

### Real life scenarios
You probably heard some scary stuff about category theory

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

Note:
Of course you can use for loops etc as you wish. Myself, I don't think I used a for loop once since I started my Scala journey - I want to make the most of Scala and i like how clean the code looks and how experssive you can get with using those transfornation functions. I like to be free from `x.length`'s, `i = 0`'s and `i++`'s - mutability!

----
References:<br>
_Functional programming in Scala: Simplified_ by A. Alexander<br>
_Scala Cookbook_ by A. Alexander