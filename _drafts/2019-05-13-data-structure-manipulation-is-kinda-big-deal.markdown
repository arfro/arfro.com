---
layout: post
title: Data manipulation is kind of a big deal - part 1
date: 2019-05-13 13:32:20
img: transformations/factory.png 
tags: [transformation, map, flatMap, filter, collect, functional-programming]
---
Data manipulation and transformation is a kind of a big deal. Now that I shared that recursion is important but also mentioned there is other ways to achieve certain goals let's look into that. This will be a first post in a mini series on data manipulation and transformation. I will focus on <b>collections</b> mainly - there is other important usages but I will focus on most intuitive stuff first.
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

Let's start with `map`, `flatMap`, `collect` and `filter`.<br>

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
You can think of `map` as "apply this function to each element of this collection". 

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
We can now `map`:
{% highlight scala %}
val upperCasedListsOfUsers = 
allUsers.map(listOfStringUpCase) // List(List("ANN", "BETTY"), List("CARO", "CIARA"))
{% endhighlight %}
...and then `flatten`:
{% highlight scala %}
upperCasedListsOfUsers.flatten // List(ANN, BETTY, CARO, CIARA)
{% endhighlight %}
... instead of all this noise we could just use `flatMap`:
{% highlight scala %}
allUsers.flatMap(listOfStringUpCase) // List(ANN, BETTY, CARO, CIARA)
{% endhighlight %}

<b>Summary</b><br>
You can think of `flatMap` as "apply this function to each element of this collection and then flatten the result".  

<b>Important note - flatMap for `List[Option[_]]`</b>

`flatMap` has a special usage for a list of options - it will "remove" `None` values:
{% highlight scala %}
List(Some("hello"), None, None, Some("world")).flatMap(x => None) // List()
{% endhighlight %}
..unlike `map`:
{% highlight scala %}
List(Some("hello"), None, None, Some("world")).map(x => None) 
// List(None, None, None, None)
{% endhighlight %}
But in regards to above `map` - if we use `flatten` afterwards the result will be obviously the same as from above `flatMap`
{% highlight scala %}
List(Some("hello"), None, None, Some("world")).map(x => None).flatten // List()
{% endhighlight %}

## filter
Returns items that meet a given predicate:
{% highlight scala %}
List(1, 2, 3, 4).filter(_ > 3) // List(4)
{% endhighlight %}


## collect
Nothing more than a mix of `filter` and `map`. The syntax might seem a bit complicated as collect takes a partial function (post on that will come soon):
{% highlight scala %}
List(1, 2, 3, 4).collect {
    case i if(i > 3) => i * 3
} 
// List(12)
{% endhighlight %}

<b>Summary</b><br>
You can think of `collect` as "filter values from this collection and for those that match apply this function"




## Realistically, how often do you use those instead of loops etc?
<b>Every single day</b>, without a failure. I think `map`, `flatMap` and `filter` are the first things you learn. I remember my very first Scala code, and by very first I literally mean first lines. I had this gorgeous for loop ready to go and someone pointed out in my code review that my 9 lines of code could be replaced with less than 10 characters... Of course, I was sold!

Here is a few ideas on how to solve some easy problems using what I explained today:

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
Of course you can use for loops and imperative ways if you wish for solving all of above problems too. I will never ever encourage this though. With incredibly powerful Collections API that Scala offers it would be like hooking up a snow plougher to a Ferrari. Myself, I don't think I used a for loop once since that first code review I mentioned - I want to make the most of Scala and I like how clean the code looks and how expressive you can get. So I am officially freed from `i = 0`s, `i > 0`'s and `i++`'s!
