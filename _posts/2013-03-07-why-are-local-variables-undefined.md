---
layout: post
title: Why are local variables undefined
---
The result of the following simple javascript codes may be a bit confusing.

{% highlight javascript %}
var name = 'Jason';

//Anonymous function 1 (AF1)
(function() {
    console.log(name); //undefined
    var name = 'Eric';
})();
{% endhighlight %}

So what is the exact reason behind? Some programmers might argue that it is because the anonymous functions do not share the same scope with the global environment outside, so the variable name defined outside is invisible to the anonymous function here. It is not true. The codes below prove my answer.
<!-- more -->
{% highlight javascript %}
//Anonymous function 2 (AF2)
(function() {
    console.log(name); //Jason
})();
{% endhighlight %}

The rationale behind is a matter of how javascript systematically manages the scope chain and identifiers.

## The [[Scope]] property, scope chain, and execution context
`[[Scope]]` is one of the inaccessible property of a Function object, which refers to a `scope chain` that contains a list of `objects` representing the scopes. Each of the scopes (a variable `object`), in which the function is created, contain a collection of `variables` accessible for the function. Once a function is parsed, the scope chain is populated with a `global object` including all globally defined variables. Taking the anonymous function 1 (AF1) as an example, the `scope chain` of AF1 is generated as follows when AF1 is created:

![Scope Chain]({{ site.image_baseurl }}/SC11.png)

`Execution context` (an object) is created once the function starts to be executed, containing a `scope chain` that is copied from the existing `scope chain` in `[[Scope]]` property. Then a `activation object` including all local variables of the function is generated and pushed to the head of `scope chain` (See below).

![Scope Chain]({{ site.image_baseurl }}/SC21.png)

During the execution of the function, each identifier is searched from the front of the scope chain to the end (object by object). It is such searching process that affects the performance of the code.

## The exact reason
The reason is apparently seen from the above figures. In AF1, due to the declaration of the local variable `name`, the identifier `name` is first located in the `activation object`. Before it was assigned as 'Eric', it remains `undefined` (A variable is declared in parse-time (remaining undefined) and defined in run-time). With regard to AF2, the `name` is found in the `global object` so the value can be obtained.