---
layout: post
title: Understand recursion to understand recursion
date: 2019-03-04 13:32:20
img: recursion/recursion.jpeg 
tags: [functional-programming, recursion]
---

 We can imagine that any non-trivial program will at some stage have to loop over something and since we spoke of immutability in functional programming so much we might feel a bit hesitant to use our regular loops... Recursion comes to rescue.


### What's recursion, how does it actually work and how does it relate to immutability?

Recursion is a function that calls itself until an exit condition is met. It is a way to break down a large problem into smaller problems which when all solved will also solve the large problem. To put that into a real life perspective: my friends would know that my hair used to be quite long and so drying it was a form of a recursive call. Dry my hair for a minute. Check if my hair is dry. If it's dry, stop. Else, dry my hair for one minute.<br>

#### How recursion works on stack

Before we jump into examples I would like to talk a little about stack as this is how recursion works internally. Let's dig a little under the hood of a function call.

Stack is a place in memory where temporary variables created by function calls are stored. Stack is a last-in first-out (LIFO) data structure which means the last element added will be the first element removed. Anytime a function is called for the first time it creates a block in the stack where all of it's arguments, any local variables it creates (and more) is pushed. Once the function exits, all elements on the stack are popped (last element pushed in will be first to be popped (LIFO)) and the block is erased. To visualise it:

{% highlight scala %}
 def fctn = {
    val a = "a"
    val b = "b"
 }
{% endhighlight %}

Given the above function this is want happens:

On `fctn` call:<br>
1. Create a stack block for `fctn`<br>
1. Push variable - `a = "a"`<br>
1. Push variable - `b = "b"`<br>

On `fctn` exit:<br>
1. Pop variable (`b`)<br>
1. Pop variable (`a`)<br> 
1. Erase `fctn` block<br>

![stack block]({{site.baseurl}}/assets/img/recursion/stack.jpg)

It's a good time now to bring in a book classic recursive function: factorial.<br>
{% highlight scala %}
 def factorial(n: Int): Int = {
    if(n <= 1) 1 // exit condition
    else n * factorial(n - 1) // recursive call to itself
 }
{% endhighlight %}

We can see this function consists of two parts:
* exit condition (a.k.a. base case)
* recursive call which will be called until exit condition is met

Now let's see how factorial actually works when called. You see the recursive calls that go "down" (onto the stack) and return values that go "up" (off the stack) once calling function is evaluated (a bit simplified but that's essentially how that works). As I mentioned before we can see here well how working out a smaller problem (factorial(1)) leads to working out the large problem (factorial(4)).<br>
![stack block 2]({{site.baseurl}}/assets/img/recursion/stack2.jpg)


With the above explained it is easy to notice how important the exit condition is when it comes to recursion. If not for the exit condition above we would continue calling the factorial function infinitely. 

### Head recursion
Head recursion is a recursion that calls itself with an updated argument until the exit condition is met. A great example is our factorial function. As you can imagine the stack size is limited and therefore if we were to call `factorial(1234)` the code will throw StackOverflowException - there is no more space left on the stack for yet another call. This is something that we should avoid and so an alternative would be <b>tail recursion</b>.


### Tail recursion
Tail recursion is a way to avoid stack overflow exceptions. Tail recursive function is a function where the very last call is the call to itself. There is an accumulator that allows to pass a calculated value to the next recursive call as we go. In Scala they can be annotated with `@tailrec`. 
<br>
<br>
So let's rewrite our factorial:
{% highlight scala %}
 @tailrec
 def factorial(n: Int, accumulator: Int): Int =
    if(n <= 1) accumulator // exit condition
    else factorial(n - 1, n * accumulator) // recursive call to itself
 }
{% endhighlight %}

A couple of differences to head recursive version:<Br>
* Scala will allow you to annotate this code as tail recursion
* there is an accumulator
* there will be no "pyramid" of calls onto the stack like in head recursion because we are accumulating the calculated value as we go and pass it immediately on to the next recursive call. <br>

The calls will look like this...

![stack block 2]({{site.baseurl}}/assets/img/recursion/stack3.jpg)

...which resembles a while loop a little bit...<br>

<b>PLOT TWIST!</b> In Scala tail recursive functions are actually optimised to be while loops under the hood.

#### What.. So does recursion then help with sticking to immutability or not?
  As each <b>head recursive</b> call is pushed onto a stack and then evaluated on function exit there is actually no mutation of variables, only function calls. It can cause stack overflow though, e.g. if we are getting a factorial of a large number so generally it is a better idea to write <b>tail recursive</b> functions. As I mentioned before in Scala they are actually automatically optimised to become while loops under the hood so technically tail recursion is not really a fully immutable solution. 
  <br><br>
  So it comes down really to what would be your personal choice - recursion or a loop. I have quite a bit of confidence in Scala's inner workings on optimization and so I go for tail recursive solutions. Apart from the Scala confidence part I find them more entertaining, generally more consistent with the rest of the codebase, looking more concise and I also don't have to worry about little bugs I could introduce in getting my own loops to work (like off-by-1 or so). 

### Real life
I can sure write a factorial function or list some directories, but where could I possibly use recursion in real life?<Br>

Recursive data structures - data structure that has references to itself. have to be iterated over recursively. E.g. Any tree like structure, maybe employees that have a list of subordinates that have a list of subordinates that have no subordinates <br>

I would like to stress the importance of writing tests for recursive functions, no matter how bulletproof they seem to be. With great power comes great responsibility and nothing makes you sleep at nice as soundly as a well tested recursive code in production.

### Summary 
I would like to stress two rules of thumb for writing recursive call here:
* write your exit condition before writing anything else (here: `if n <= 1`)
* make sure you are moving towards the exit condition in your recursive calls (here: `factorial(n - 1)` - moving towards `1`)

But remember - as one wise woman once said: 
>Where there is people practising recursion there stacks shall overflow and functions shall never exit.

I've been there and a lot of programmers have too. It's difficult to get recursion right at first. But I promise if you commit to solving 30 problems recursively you will start seeing a new ways of thinking emerging.

Recursion is a very helpful tool to have in your functional programming journey. You will likely not use it daily, but in my career I did have to write quite a number of recursive functions so I can't stress enough how important it is to get familiar with it. I promise that with time it will become more and more clear!

----

References:<br>
_Category theory for programmers_ by B. Milewski<br>
_Basic Category Theory_ by T. Leinster<br>
_Scala Cookbook_ by A. Alexander
