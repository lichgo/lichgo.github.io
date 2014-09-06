---
layout: post
title: Garbage collection in JavaScript memory management
---
The memory management seems a neglectable issue for JavaScript programmers since the garbage collector lies there to automatically release the unnecessary memory. However, it is also very easy to write some code that leads to memory leak, especially when dealing with the event delegation in DOM. An overview of the mechanism of **Garbage Collection** in JavaScript is beneficial when we want to probe our code to inspect some potential memory problems.

Consulting the article [here](https://developer.mozilla.org/en-US/docs/JavaScript/Memory_Management), I made a summary about the principles used to release the allocated memory. There are three major criteria for judging if a variable can be destroyed, indicating different number of variables whose memory should be collected.

![GC]({{ site.image_baseurl }}/gc.png)
<!-- more -->
## Explanation
1. **Not needed anymore** is the most generic and intuitive manner and covers the largest scope of variables that should be destroyed. However, it is undecidable.
(2) **Unreachable***
(3) **No variables referencing to it** explicitly states the condition at code level such that the programmers are able to manipulate it. However, it still not exhaustive to find all collectable memory. Some variables to which are referenced by others can also be destroyed. One typical example is the at the presence of circular reference where the references form a closed-loop.
(4) Comparing **Unreachable*** and **No variables referencing to it**, it can be obviously seen that **No variables referencing to it** leads to **Unreachable** while the opposite is not true. This is also depicted in the figure by illustrating that **No variables referencing to it** is the subset of **Unreachable**.
(5) Now that under **Unreachable**, even the memory trapped in circular references can be released if the variables are no longer accessible. Mark-and-sweep algorithm is implemented in modern browsers to manage releasing memory.

## To conclude
To sum up, despite the fact that some browsers are capable of searching all **Unreachable** variables to "collect the garbage", it is advisable to avoid any possible memory leak problems when writing the js code.