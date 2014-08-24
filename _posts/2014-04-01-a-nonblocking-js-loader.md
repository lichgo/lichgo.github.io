---
layout: post
title: A non-blocking js loader
---
As browsers parse and render page code sequentially, the parsing and execution of Javascript (whether inline or external) code might block rendering the page and downloading other resources (e.g. images) if the js code was not properly placed. Creating dynamic script elements, due to its intrinsic asynchronous loading pattern, has been widely recognized as an efficient approach to nicely work around this problem (non-blocking). 

In order to load a collection of external js files by this manner, I wrote a simple loader that takes an array of filenames, in which the js files are parsed and executed in order.

{% highlight javascript %}
//Dynamically load js files, taking an array.
function loadJS(fileList) {
    var i = 0,
        doc = document,
        h = doc.getElementsByTagName('head')[0];
    
    l(i);

    function l(i) {
        var js = doc.createElement('script');
        js.type = 'text/javascript';

        var re = function() {
            i < fileList.length - 1 ? l(i + 1) : console.log('All scripts loaded.');
        }

        if (js.readyState) {
            js.onreadystatechange = function() {
                if (js.readyState == 'loaded' || js.readyState == 'complete') {
                    js.onreadystatechange = null;
                    re();
                }
            }
        } else {
            js.onload = re;
        }

        js.src = fileList[i];
        h.appendChild(js);
    }
}
{% endhighlight %}

One line of code is enough to load all files:

{% highlight javascript %}
loadJS(['a.js', 'b.js', 'c.js']);
{% endhighlight %}