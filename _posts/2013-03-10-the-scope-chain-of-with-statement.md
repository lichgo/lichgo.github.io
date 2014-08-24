---
layout: post
title: The scope chain of with statement
---
When executing the codes wrapped in `with(object)` statement of Javascript, the execution context's scope chain contains at least three variable objects, with the passed `object`'s scope on the top of the scope chain while the activation object and global object following. The codes below demonstrate how the program operates under the scope chain.

{% highlight javascript %}
var person = {
    name: 'Sun',
    person: 'another person'
}

with(person) {
    var name;
    console.log(name); //Sun

    var _inner = '_inner';
    console.log(_inner); //_inner

    console.log(person); //another person
    delete person;
    console.log(person); //{ name: 'Sun' }
}
{% endhighlight %}