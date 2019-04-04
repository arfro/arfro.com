---
layout: post
title: Understand recursion to understand recursion
date: 2019-03-04 13:32:20
img: recursion/recursion.jpeg 
tags: [functional-programming, recursion]
---

As I mentioned in my previous post on functional programming one of the main things to look out for when writing functional code is immutability. As any non-trivial program will at some stage have to iterate over something and we want to guarantee that this iteration will not break full purity of our functions by introducing mutable data in loops. Recursion is one way to achieve that.


### What's recursion, how does it actually work and how does it relate to immutability?

Recursion is a function that calls itself until an exit condition is met. It is a way to break down a large problem into smaller problems which when all solved will also solve the large problem. To put that into a real life perspective: some of my friend would know that my hair is quite long and so drying it is a form of a recursive function. Dry my hair. Check if my hair is dry. If it's dry, stop. Else, dry my hair.<br>

#### how recursion works on stack

Before we jump into examples I would like to talk a little about stack as this is how recursion works internally. When a function is called its execution context is pushed onto a stack and popped off the stack once the execution is finished.

Let's start with a classic recursive problem: factorials.<br>
{% highlight scala %}
 def factorial(i: Int): Int = {
    if(n <= 1) 1
    else n * factorial(n - 1)
 }
{% endhighlight %}
  
  This classic function is a great way to learn how to write recursively. The rule of thumb for writing recursive functions is to always have your exit condition written before getting to recursive cases. What does that mean?
  
  
#### how is recursion immutable
  
  
  
### Head recursion

### Tail recursion

### StackOverflowException
Too much data on the stack

### Real life tips

### Testing
I would like to stress the importance of writing tests for recursive functions, no matter how bulletproof they seem to be. With great power comes great responsibility and nothing makes you sleep at nice as soundly as a well tested recursive code in production.

                         
![function composition]({{site.baseurl}}/assets/img/functional-programming/category-theory-small.png)

### Summary 
Recursion is a very helpful tool to have in your functional programming journey. You will likely not use it daily, but in my career I did have to write a number of recursive functions so I can't stress enough how important it is to get familiar with it. I promise that with time it will become more and more obvious on how to use it!

----

References:<br>
_Category theory for programmers_ by B. Milewski<br>
_Basic Category Theory_ by T. Leinster<br>
_Scala Cookbook_ by A. Alexander
