---
layout: post
title: Using slice method for objects
---
In Javascript, an object doesn't hold the `slice()` method, while arrays and strings do. Employing `apply` or `call` can easily make `slice()` work for objects, e.g. `Array.prototype.slice.call(arguments, 1)`, which is a commonly used manner to cope with the `arguments` object in a function. However, the following usage is a bit weird:

{% highlight javascript %}
//an object with non-numerical keys
var obj1 = {
    name: 'lichgo',
    age: 24
};
[].slice.call(obj1, 1);  //[]

//another object with numerical keys (the same as arguments object)
var obj2 = {
    0: 'lichgo',
    1: 24
};
[].slice.call(obj2, 1);  //[]
{% endhighlight %}

The reason is obviously related to how `slice` method runs behind, and the different results of taking an usual object and an `arguments` object are attributed to the fact that `arguments` is a special array-like object with an intrinsic property: `length`.

According to ECMA-262(15.4.4.10 Array.prototype.slice), the key steps of calling this method include:

``
1. Let O be the result of calling ToObject passing the this value as the argument.
2. Let A be a new array created as if by the expression new Array() where Array is the standard built-in constructor with that name.
3. Let lenVal be the result of calling the [[Get]] internal method of O with argument "length".
...
10. Repeat, while k < final
a. Let Pk be ToString(k).
b. Let kPresent be the result of calling the [[HasProperty]] internal method of O with argument Pk.
c. If kPresent is true, then
i. Let kValue be the result of calling the [[Get]] internal method of O with argument Pk.
ii. Call the [[DefineOwnProperty]] internal method of A with arguments ToString(n), Property Descriptor {[[Value]]: kValue, [[Writable]]: true, [[Enumerable]]: true, [[Configurable]]: true}, and false.
...
11. Return A.
``

When calling `[].slice(obj, 1)`, the below code is executed:

{% highlight javascript %}
O = ToString(obj);
A = new Array();
lenVal = O.getProperty('length');
//Next, insert elements into A in a while loop based on the value of lenVal
{% endhighlight %}

As an usual object has no `length` property, the while loop is not executed so it returns an empty array []. If we attach this property manually to the object, the method works.

{% highlight javascript %}
//another object with numerical keys (the same as arguments object)
var obj2 = {
    0: 'lichgo',
    1: 24
};
obj2.length = 3;
[].slice.call(obj2, 1);  //[24, ], values with non-numerical keys are returned as undefined, as explained below.

obj2.length = 10;
[].slice.call(obj2, 1);  //[24, , , , , , , , ], the empty elements are undefined as the obj doesn't have that many elements
{% endhighlight %}

As it only uses the numerical key to search the object (i.e. i. Let kValue be the result of calling the [[Get]] internal method of O with argument Pk.), the following results can be expected:

{% highlight javascript %}
//an object with non-numerical keys
var obj1 = {
    name: 'lichgo',
    age: 24
};
obj1.length = 3;
[].slice.call(obj1, 1);  //[ , ]
{% endhighlight %}