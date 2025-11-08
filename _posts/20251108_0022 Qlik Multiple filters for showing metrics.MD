---
layout: post
title: Qlik Multiple filters for showing metrics
tags:
- Qlik
- Filters
comments: true
---
Dificulty ★★★☆☆

IN PROGRESSS!!


In this tutorial i will show you how diffrent variables can interect to hide/show/calculate metric.

![varables](/img/20251108_0022/10_variables.png) <br />
![varables](/img/20251108_0022/11_values.png) <br />
![varables](/img/20251108_0022/12_metric_variable.png) <br />
*Note Base scenario and Comparison scenario are Alternative states of same atribute<br />
<br />
Create new variable that will be used for metric selection<br />
![varables](/img/20251108_0022/1.png) <br />

From object select 'Variable input' 
![varables](/img/20251108_0022/2.png) <br />

give it previusly deffined varable name and select how you want to show it.
![varables](/img/20251108_0022/3.png) <br />

Define posible values that you are going to show in filter.
![varables](/img/20251108_0022/4.png) <br />

Define filter name
![varables](/img/20251108_0022/5.png) <br />

Before continuing lets make a small test if evryhing is working
The definition for fist metric in this case 'Balance' can be show if filter selection is 'Balance' or 'All'<br />
So change the 'Show column if' setting to<br />
{% highlight metric %} 
WildMatch(vMetricShown, 'Balance', 'All')
{% endhighlight %}
![varables](/img/20251108_0022/6.png) <br />

Create the second variable
![varables](/img/20251108_0022/7.png) <br />

Create new 'Variable input' and <br />
define posible values that you are going to show in filter.<br />
![varables](/img/20251108_0022/13_Variable2.png) <br />

Change the optin 'Show column if' for first metric to 
{% highlight metric %} 
WildMatch(vMetricShown, 'Balance', 'All') and WildMatch(vCompareMode, 'Values', 'All')
{% endhighlight %}
![varables](/img/20251108_0022/8.png) <br />

*Optional
in load set the defalt variable state when opening dossie<br />
![varables](/img/20251108_0022/9.png) <br />
{% highlight metric %} 
SET vMetricShown = 'All';<br />
SET vCompareMode = 'All';
{% endhighlight %}

Aditionaly 
If you want to filter other atriutes normaly don't forget to put '* ?' in metric definition after alternative state.
Sum({[Alt BASE scenario] <span style="color:red">* $</span>} if(IsNull(MEAS_AMOUNT_01), 0, MEAS_AMOUNT_01))





