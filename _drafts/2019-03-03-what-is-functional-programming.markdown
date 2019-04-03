---
layout: post
title: What is functional programming?
date: 2019-03-03 13:32:20
img: functional-programming/category-theory-2.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [functional-programming, category-theory, oo-programming]
---

Sooner or later every programmer comes across functional programming paradigm. Some of us get to work with it and some of us only hear about it, some of us maybe even try it out and can't help but wonder - why is it so hype now? I was wondering this myself when I started. Functional programming at first can make little sense - in fact in a way it can even make you feel like it goes against what you've learnt before. <br><br>
In this post I will try to pinpoint a few basics that will allow to understand what functional programming is, what it bases on and why is it all over the news these days.

In this post I want to cover the following:
- what's category theory - absolute basics
    - explain actual definition. Explain it's like reasoning about relations and transformations between sets (cateogries) 
    rather than elements of sets: when you think of multiplication 
    you don't necessarily think of numbers you want to multiply, e.g. you can imagine if x and y belong to negative numbers category
    then you can figure x * y will always belong to positive numbers category - you dont need to know exactly the numbers 
    - !!! can compose (going from x -> d -> f) < the root of category theory
    - dont need to know category rheory to write funtinoal code, once youre good with functinoal category theory will start
    making more sense on its own
- what's functional programming
    - think of code like algebra, you wouldn't expect side effects from 1 + 1
    - don't mutate any state (like in algebra x = 1 and y = 5 you know x + y is always 6. If you mutate then its not
     deterministic
     - obviously not a rule but whats easy in OO (for loops instad of recursion) is a bit more difficult in fp and whats easy in fp (e.g. composing? deterministic values?) is not tht east in oo
    and will cause problems)
    - separate data from functions (like in category theory nodes and functors)
    - compose your functions
- why is functional programmiong on hype now
    - multicore processors
    - parallism works well with immutability and associativity

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
