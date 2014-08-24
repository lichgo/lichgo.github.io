---
layout: post
title: Object is a factory in JavaScript
---
`Object` is basically a factory pattern that generates different types of variables. See the code below:

{% highlight javascript %}
var obj = Object();  // equals to new Object();
var num = Object(2);
var str = Object("lichgo");
var boo = Object(false);
{% endhighlight %}

Magic is real!

{% highlight javascript %}
obj.constructor === Object;  //true
num.constructor === Number;  //true
str.constructor === String;  //true
boo.constructor === Boolean; //true
{% endhighlight %}