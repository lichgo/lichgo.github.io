---
layout: post
title: Design a dynamic price calculator
---
Price calculation is an essential part of online stores. The value of a price changes under various conditions, such as adding tips, some percentages off, or tax refund. It is usually re-calculated in run-time once online customers select different plans or purchase extra services. In this sense, a dynamic calculator that is flexible enough to be called by client programmers is appealing.

Air ticket is a typical example. The price at first sight is a raw price, remaining to be updated by adding tax, insurance, etc. In essence, adding features to a price acts like extending properties or methods to an object. Using prototype chain can accomplish it. The code below defines some features at the outset. These "features" can be easily integrated into an existing object by a shared method: addFeature(), which creates a new object inherited from the existing object.

{% highlight javascript %}
// Define a TicketOrder class and a prototypal method: getPrice()
function TicketOrder(price) {
    this.price = price;
}

TicketOrder.prototype.getPrice = function() {
    return this.price;
}

// Predefined decorators: rewrite the getPrice() method
TicketOrder.features = {};
TicketOrder.features.tax = {
    getPrice: function() {
        return this.uber.getPrice() * 1.2;
    }
}
TicketOrder.features.insurance = {
    getPrice: function() {
        return this.uber.getPrice() + 30;
    }
}

// Add features
TicketOrder.prototype.addFeatures = function(type) {
    // Create a temp class F and make its instances descendants of "this" (the existing price)
    var F = function() {};
    F.prototype = this;
    var obj = new F();
    obj.uber = this;

    // Copy the methods in the feature to the newly created object (price)
    var fea = this.constructor.features[type];
    for (var key in fea) {
        if (fea.hasOwnProperty(key)) {
            obj[key] = fea[key];
        }
    }

    return obj;
}

// Test
var order = new TicketOrder(200);
console.log(order.getPrice());    // 200
order = order.addFeatures('tax');
console.log(order.getPrice());    // 240
order = order.addFeatures('insurance');
console.log(order.getPrice());    // 270
{% endhighlight %}

Reference:  
Stoyan Stefanov, *[JavaScript Patterns: Build Better Applications with Coding and Design Patterns](http://shop.oreilly.com/product/9780596806767.do)*, O'Reilly Media, Sept. 2010.