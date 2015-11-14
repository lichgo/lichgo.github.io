---
layout: post
title: How to get adjusted stock prices from Bloomberg API
---

Quantitative research or backtest requires masses of historical data. When it comes to test a strategy on equities, adjusted stock prices would be more preferrable in order to have an accurate profit and loss, without adjusting position and the parameters of the model.

One of my data collection program, which automatically fetches data from Bloomberg using its API, supports getting adjusted prices. There are only three lines of code that enable this feature, with setting three parameters `adjustmentNormal`, `adjustmentAbnormal`, and `adjustmentSplit` to `true`.


{% highlight java %}
Service refDataService = session.getService("//blp/refdata");
Request request = refDataService.createRequest("HistoricalDataRequest");
.....
// Remember to set the three parameters to true
request.set("adjustmentNormal", true);
request.set("adjustmentAbnormal", true);
request.set("adjustmentSplit", true);
{% endhighlight %}

Bear in mind that `adjustmentNormal`, `adjustmentAbnormal`, and `adjustmentSplit` should be **explicitly** set to `false` if you want original stock price.