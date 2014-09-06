---
layout: post
title: Update Node.js on Ubuntu
---
I tried to use npm to install a node module globally on Ubuntu. Some errors occurred and I found the major reason was that the version of node.js was a bit too old. Fortunately, it is quite straightforward to update node.js. In Terminal issue the following command lines: 

{% highlight bash %}
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:chris-lea/node.js
sudo apt-get update
sudo apt-get install nodejs
{% endhighlight %}
<!-- more -->
In fact, the problem originated from the failure to locate the imported module in a node program. Prior to this problem, I have installed the module locally in the directory of the program. Installing the module globally in **/usr/lib/node_modules** still wasn't enough to fix it. As suggested on Stack Overflow, I re-installed the module locally. It works now! 

**It is an option (or might be better?) to install the required modules on both places**, although I am still confused about the reasons behind.

Reference:  
[How to install the latest node.js on Ubuntu?](http://slopjong.de/2012/10/31/how-to-install-the-latest-nodejs-in-ubuntu/)

