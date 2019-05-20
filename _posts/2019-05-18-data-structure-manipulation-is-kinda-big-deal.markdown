---
layout: post
title: Data manipulation is kind of a big deal - part 1
date: 2019-05-18 13:32:20
img: transformations/factory.png 
tags: [transformation, map, flatMap, filter, collect, functional-programming]
---
Data manipulation and transformation is a kind of a big deal. Now that I shared that recursion is important but also mentioned there is other ways to achieve certain goals let's look into that. This will be a first post in a mini series on data manipulation and transformation. I will focus on <b>collections</b> mainly for now - there is other important aspects to this topic but let's start with most intuitive stuff first.
<br>

Keeping that in mind let's recall Scala basic collections real quick:
* List
* Map
* Set
* tuple
* Option (a collection that's either empty or has one item, fabulous into from Alvin Alexander [here](https://alvinalexander.com/scala/using-scala-option-some-none-idiom-function-java-null))

I find there is usually a couple of basic things I want to do with collections (apart from trivial stuff like getting size etc.):
* get items that meet a certain condition
* do something with every item
* do something with items that meet a certain condition
* check if there is or isn't an item that meets a certain condition

Keeping that in mind let's start with `map`, `flatMap`, `collect` and `filter`.<br>

## map
You can call `map` on all the collections. It applies a passed function to each element and returns a collection of transformed elements.<br>

{% highlight scala %}
def divideByTwo(number: Int) = number / 2

List(10, 20, 30).map(divideByTwo) // List(5, 10, 15)
{% endhighlight %}
... the function can also be anonymous, you will <b>very</b> often see code like:
{% highlight scala %}
List(10, 20, 30).map(nr => nr / 2) // List(5, 10, 15)
{% endhighlight %}
...or even more often with syntactic sugar for anonymous function like this:
{% highlight scala %}
List(10, 20, 30).map(_ / 2) // List(5, 10, 15) 
{% endhighlight %}

Of course the function used in `map` can be a function that goes from (in our case) `Int` to any other type - sky is your limit. So let's maybe try a function that goes from `Int` to `String`?
{% highlight scala %}
List(10, 20, 30).map(_.toString) // List("5", "10", "15") 
{% endhighlight %}

<b>Summary</b><br>
You can think of `map` as "apply this function to each element of this collection and return the result". 

<b>Important note - map for Option</b>

`map` has a special usage for `Option` - it will only execute passed function on `Some`:
{% highlight scala %}
val someString: Option[String] = Some("hello")
val noneString: Option[String] = None
someString.map(_.length) // Some(5)
noneString.map(_.length) // None
{% endhighlight %}
Can you see already how handy it could be for error handling? There will be a separate post on error handling and `Option` will be one of the stars of the episode.

## flatMap
`flatMap` is nothing more than calling `map` first and then `flatten`. Why would it be useful?

In collections, `flatMap` is a lifesaver when you find yourself with a type of nested types like `List[List[_]]` or maybe `Option[Option[_]]`.

Let's say we get a list of users in an unintuitive type of `List[List[String]]`:
{% highlight scala %}
val allUsers = List(List("ann", "betty"), List("caro", "ciara"))
{% endhighlight %}
Let's also say that we get a requirement to list all the names as one list but with all names uppercase. 
To make the end code more readable we could first we could create a little function:
{% highlight scala %}
def listOfStringUpCase(list: List[String]) = list.map(_.toUpperCase)
{% endhighlight %}
So we could now `map`, right?
{% highlight scala %}
val upperCasedListsOfUsers = 
allUsers.map(listOfStringUpCase) // List(List("ANN", "BETTY"), List("CARO", "CIARA"))
{% endhighlight %}
...unfortunately that leaves us with a `List[List[String]]` again. So now it's time to `flatten` - to "merge" our nested `List`s and end up with a single `List`:
{% highlight scala %}
upperCasedListsOfUsers.flatten // List(ANN, BETTY, CARO, CIARA)
{% endhighlight %}


But what about `flatmap`? Well, instead of all this noise we could just use `flatMap` instead for a very intuitive and elegant solution:
{% highlight scala %}
allUsers.flatMap(listOfStringUpCase) // List(ANN, BETTY, CARO, CIARA)
{% endhighlight %}

<b>Summary</b><br>
You can think of `flatMap` as "apply this function to each element of this collection, then flatten the result and return transformed collection".  

<b>Important note - flatMap for `List[Option[_]]`</b>

`flatMap` has a special usage for a `List[Option]` - it will "remove" (flatten) `None` values:
{% highlight scala %}
List(Some("hello"), None, None, Some("world")).flatMap(_.map(_.toUpperCase)) 
// List(HELLO, WORLD)
{% endhighlight %}
..unlike `map`:
{% highlight scala %}
List(Some("hello"), None, None, Some("world")).map(_.map(_.toUpperCase)) 
// List(Some(HELLO), None, None, Some(WORLD))
{% endhighlight %}
But in regards to above `map` - if we use `flatten` afterwards the result will be obviously the same as from above `flatMap`
{% highlight scala %}
List(Some("hello"), None, None, Some("world")).map(_.map(_.toUpperCase)).flatten
// List(HELLO, WORLD)
{% endhighlight %}

## filter
This is quite trivial - `filter` does exactly what you think it does - it returns items that meet a given predicate:
{% highlight scala %}
List(1, 2, 3, 4).filter(_ > 3) // List(4)
{% endhighlight %}


## collect
A "mix" of `filter` and `map`. The syntax might seem a bit complicated as `collect` takes a partial function (post on partial functions will come soon):
{% highlight scala %}
List(1, 2, 3, 4).collect {
    case i if(i > 3) => i * 3
} 
// List(12)
{% endhighlight %}

<b>Summary</b><br>
You can think of `collect` as "filter values from this collection and for those that match apply this function"



## Realistically, how often do you use those instead of loops etc?
<b>Every single day</b>, without a failure. I think `map`, `flatMap` and `filter` are the first things you learn. I remember my very first Scala code, and by very first I literally mean first lines. I had this gorgeous for-loop ready to go and someone pointed out in my code review that my 9 lines of code could be replaced with less than 10 characters... Of course, I was sold!

Here is a few ideas on how to solve some easy problems using what I explained today, compare them in your head to solutions that wouldn't use `map`, `flatmap`, `filter` or `collect`. Below solutions are much more compact, aren't they?

### list all odd numbers
{% highlight scala %}
val list = List(1, 2, 3, 4)
list.filter(_ % 2 != 0)
{% endhighlight %}

### find the length of the shortest word in a sentence
{% highlight scala %}
val sentence = "How long is the shortest word?"
sentence.split(" ").map(_.length).min // 2 
{% endhighlight %}

### duplicate all elements in a list
{% highlight scala %}
val list = List(1, 2, 3, 4)
list.flatMap(element => List(element, element))
{% endhighlight %}

### change all names that start with A to uppercase and return them only
{% highlight scala %}
val list = List("anna", "brad", "aga", "steve")
list.collect {
    case name if(name.startsWith("a")) => name.toUpperCase
}
{% endhighlight %}


## Final note
Of course you can use for loops and imperative ways if you wish for solving all of above problems too. I will never ever encourage this though. With incredibly powerful Collections API that Scala offers it would be like hooking up a snow plougher to a Ferrari. Myself, I want to make the most of Scala and I like how clean the code looks and how expressive you can get. How satisfying to have those one liner solutions...
