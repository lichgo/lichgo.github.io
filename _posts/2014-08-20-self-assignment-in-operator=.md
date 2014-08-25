---
layout: post
title: Self assignment in operator=
---
In C++, it is a good practice to evaluate if the passed instance is exactly itself at the very beginning of defining a operator= method:

{% highlight c++ %}
class PriceCalculator;
class Stock {
    // ...
    Stock& operator=(const Stock& rhs);
    PriceCalculator *pc;
}

Stock& Stock::operator=(const Stock& rhs) {
    if (this == &rhs) return *this;     // best practice
    delete pc;
    pc = new PriceCalculator(*rhs.pc);  // still problematic!
    return *this;
}
{% endhighlight %}

There is still a flaw in the above implementation. If an exception occurs at `pc=new PriceCalculaotr(*rhs.pc)`, the memoryof the original `pc` has been emptied. To fix this, **never clear the data of the pointers too early**.

{% highlight c++ %}
Stock& Stock::operator(const Stock& rhs) {
    if (this == &rhs) return *this;
    PriceCalculator *pc_orig = pc;  // copy a pointer
    pc = new PriceCalculator(*rhs.pc);
    delete pc_orig;     // now it's safe to clear the data
    return *this;
}
{% endhighlight %}