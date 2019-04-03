---
layout: post
title: What is functional programming?
date: 2019-03-03 13:32:20
img: functional-programming/category-theory-2.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [functional-programming, category-theory, oo-programming]
---

Sooner or later every programmer comes across functional programming paradigm. Some of us get to work with it and some of us only hear about it, some of us maybe even try it out and can't help but wonder - why is it so hype now? I was wondering this myself when I started. Functional programming at first can make little sense - in fact in a way it can even make you feel like it goes against what you've learnt before. If you're anything like I was upon starting learning about it you could perhaps share a common enough misconception that you need to know maths very well first or that knowing functional programming is a silver bullet or even "the ultimate solution".<br><br>
In this post I will try to pinpoint a few basics that will allow you to understand what functional programming really is, what it is not, what it bases on, why people mention maths in its context so much, why you probably heard it's difficult and why is it all over the news these days. Bear with me and I will try to cover all of those questions. 


### It's all maths, right?

A very short answer to this question is: <b>no</b>. <br><br>
A slightly longer answer to this question is: it's a little bit closer to maths than imperative languages but it doesn't mean you need to know maths inside out to write functional code. Will knowing certain branches of maths help? Yes. Is it crucial? No. <br>Think about a builder. Is it required of them to learn about the fundamentals of architecture and civil engineering? No. Would it benefit their understanding of why certain tasks have to be done a certain way even though it seems like an overkill? Very likely yes. <br><br>
A long answer to this question is: functional programming bases on a branch of maths called _category theory_. In a <b>MASSIVE</b> oversimplification category theory is a branch of mathematics that reasons about categories and relationships between them instead of focusing on elements of categories, e.g. category theory would reason about negative numbers as an abstract idea rather than focusing on literal values like -2 or -23. Kinda like a bird's eye view where you don't see the details but patterns. <br><br>
I am not gonna dive any deeper as this could easily be a topic for a separate blog altogether - but let's agree the main take away from category theory should be <b>composability</b> - a way of assembling a number of smaller functions into a larger function.
<br><br>
                         
                         
![function composition]({{site.baseurl}}/assets/img/functional-programming/category-theory-small.png)


  Considering above image - let's say `f` is our `add` - a function called on a negative number that results in another negative number and `g` is our `abs` (absolute value) - a function called on a negative number that results in a positive number. Let's also imagine that `A` is a negative number. We can imagine that calling `f` on `A` first will results in a negative number (`B`). As `B` is a negative number we can then call `g` on it which will result in a positive number (`C`). To show composability here we could then have a larger function called `addAndGetAbsolute` which will be a function that goes from a negative number (`A`) to a positive number (`C`) - shown in the image as `g o f` (g after f), or to tailor it to our example - `abs` after `add`.

### How is functional programming different to what I know?

Given our short introduction to category theory




### Summary 

With all the above information let's try and fight a few misconceptions that I mentioned in the beginning of the post.

##### Misconception 1: Functional programming is all about maths.
##### Misconception 2: Functional programming is a silver bullet.
##### Misconception 2: Functional programming is difficult.


In this post I want to cover the following:
- what's category theory - absolute basics
    - monads, monoids, functors, semigroups, scary stuff?
    - explain actual definition. Explain it's like reasoning about relations and transformations between sets (cateogries) 
    rather than elements of sets: when you think of multiplication 
    you don't necessarily think of numbers you want to multiply, e.g. you can imagine if x and y belong to negative numbers category
    then you can figure x * y will always belong to positive numbers category - you dont need to know exactly the numbers 
    - !!! can compose (going from x -> d -> f) < the root of category theory
    - dont need to know category rheory to write funtinoal code, once youre good with functinoal category theory will start
    making more sense on its own
    - knowing category theory for a programmer is similar to a builder and architecture and civil engineering knowledge - definitely don't need to know it inside out to do the job right but knowing just a bit definitely makes understanding certain decisions and designs better
- what's functional programming
    - closer to maths but you dont need to maths guru. think of code like algebra, you wouldn't expect side effects from 1 + 1
    - don't mutate any state (like in algebra x = 1 and y = 5 you know x + y is always 6. If you mutate then its not
     deterministic
     - obviously not a rule but whats easy in OO (for loops instad of recursion) is a bit more difficult in fp and whats easy in fp (e.g. composing? deterministic values?) is not tht east in oo
    and will cause problems)
    - separate data from functions (like in category theory nodes and functors)
    - compose your functions
    - itd not only scala, you can write in cpp as well (B. Milewski in Category Theory for programmers)
- why is functional programmiong on hype now
    - even java has some lambdas
    - imperative programming is about side effects, side effect != pure functions
    - possible to compose concurrency (everything is deterministic if pure)
    - multicore processors these days, tough to parralelie non deterministic code
    - parallism works well with immutability and associativity
    
 ^ all that.. but what would it look like in practise compared to what i know?

add pic:
![I and My friends]({{site.baseurl}}/assets/img/we-in-rest.jpg)
o
quote:
>Hexagon shoreditch beard, man braid blue bottle green juice thundercats viral migas next level ugh. Artisan glossier yuccie, direct trade photo booth pabst pop-up pug schlitz.

list:
* Hexagon shoreditch beard
* Intelligentsia narwhal austin
* Literally meditation four
* Microdosing hoodie woke

References:<br>
_Category theory for programmers_ by B. Milewski<br>
_Basic Category Theory_ by T. Leinster

Credits: <br>
[M. Bagnicka](https://www.linkedin.com/in/malgorzata-bagnicka/) - thanks for your help with polishing my builder analogy :)
