---
layout: post
title:  "Why the functional?!"
date:   2018-04-29 14:09:38 +0100
categories: functional programming
---
Every programmer transitioning from good ould cozy OO can agree with me that functional programming is a special gem. It's the mathematicians that are supposed to know it. It's supposed to be super tough and full of theory. Yet I spoke to a number of developers and who are writing functional code everyday and who came from Python, PHP and hold no degree in maths and they told me it is much more natural for them to think functional once they "clicked" and they undoubtedly want to stick to it in their careers. I just had to dive deep and understand it. What is it about it? When does it "click"? I want to "click" too!

Whatever you will read in this blog is not a matter of what's better - because saying functional is better than object oriented is like saying a racing car is better than a van. And that clearly depends whether you want to participate Monte Carlo Rally or move houses.

I've been studying functional programming and also writing functional code at work for about 6 months now.
I would like to create this blog as an attempt to first of all record my journey and second of all collect all "why the functional?" moments I came across while getting to where I am now.

My very first "why the functional?" moment (I will also be calling those <b>WTF moments</b> from now on) was when I found out functional programming aims at immutability. The very first thing I learnt. So basically _this_ ain't gonna happen:
{% highlight scala %}
val number = 3
number = 2
print(number) // should be 2, right?
{% endhighlight %}

It gives an error:

{% highlight scala %}

<console> error: reassignment to val
       number = 2
         ^
{% endhighlight %}

How on earth do I iterate over collections? - Hmmm.. <b>map</b>! - Huh?<br />
How do I even print my 1 to 10? - Hmm... <b>recursion</b>! - Oh boy...<br />
How do I update object fields? - Hmm... <b>copy the object</b> with an updated field! <br />
How is this even useful? - Well, think about it. If you are a 100% sure your value will never change do you really have to worry if when two parties want to access it they could see something else? <br />

I will try to cover all of that in the coming posts.

<b>Take from this post: <br /></b>
`val number = 5` means that `number` will return `5`. Forever. There is nothing in this universe (to my knowledge) that will make `print(number)` print anything else than `5`.

_Note that there is ways (at least in Scala) to make your values mutable variables, but I really just want to discover what's cracking with all the heat around functional programming so I will skip re-introducing any old ways._
