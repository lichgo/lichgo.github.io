---
layout: post
title: The constructor property in prototype chain
---
The `constructor` property could be very useful when the type of an variable needs to be identified. In constructing a class and its instance, or even establishing inheritance by using prototypes, `constructor` deserves more attention because it can be changed by default or by you.

**An instance never has a `constructor` property unless it is manually given. When the `constructor` of an instance is accessed, it is traced in the whole prototype chain.** 

If there is no any prototype chain manually attached to the function (class), the `constructor` of an instance is the class itself.

{% highlight javascript %}
function Cat() {
    this.name = 'Tom';
}
var cat = new Cat();
console.log(cat.constructor);  //Cat
{% endhighlight %}

However, if the function' `prototype` object is enriched, the instance's `constructor` is changed.
<!-- more -->
{% highlight javascript %}
var Animal = {
    name: 'Animal',
    age: 2
}

function Cat() {
    this.name = 'Tom';
}
Cat.prototype = Animal;
var cat = new Cat();
console.log(cat.constructor);  //No longer 'Cat', 'Object' instead.
{% endhighlight %}

Now the `prototype` of the class 'Cat' has been changed and points to an object literal 'Animal', so `cat.constructor` -> `Cat.prototype.constructor` -> `Animal.constructor` -> `Object`.

The code below justifies the above-mentioned reason.

{% highlight javascript %}
Cat.prototype.constructor = Number;
console.log(cat.constructor);  //Number
{% endhighlight %}

Certainly, we can directly re-define the <em>constructor</em> property of the instance by:

{% highlight javascript %}
cat.constructor = String;
console.log(cat.constructor);  //String
console.log(Cat.prototype.constructor);  //Number
{% endhighlight %}

Note that it doesn't change Cat.prototype.constructor, which is due to the asymmetric reading and writing in prototypal inheritance.

### Supplyment (June 15, 2013)
Another example to show that the `constructor` property of an instance is searched throughout the prototype chains.

{% highlight javascript %}
var F1 = new Function();
var f1 = new F1();
var F2 = new Function();
F2.prototype = f1;
var f2 = new F2();
f2.constructor === F1;  //true
{% endhighlight %}