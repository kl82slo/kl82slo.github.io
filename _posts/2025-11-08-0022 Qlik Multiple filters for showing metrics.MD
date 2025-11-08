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


Diffrent variables can interect to hide/show/calculate metric.<br />
<br />
STORY:<br />
We needed to compare different scenarios and calculate the differences between them.<br />
However, since there can be too many metrics, it’s easy to get overwhelmed.<br />
That’s why we needed a system to filter them efficiently.<br />

*Note Base scenario and Comparison scenario are Alternative states of same atribute<br />
![varables](/img/20251108_0022/10_variables.png) <br />
 <br />
First filter <br />
![varables](/img/20251108_0022/11_values.png) <br />
 <br />
Second filter  <br />
![varables](/img/20251108_0022/12_metric_variable.png) <br />


<br />
Create new variable that will be used for metric selection<br />
![varables](/img/20251108_0022/1.png) <br />

From object select 'Variable input'  <br />
![varables](/img/20251108_0022/2.png) <br />

give it previusly deffined varable name and select how you want to show it. <br />
![varables](/img/20251108_0022/3.png) <br />

Define posible values that you are going to show in filter. <br />
![varables](/img/20251108_0022/4.png) <br />

Define filter name <br />
![varables](/img/20251108_0022/5.png) <br />

Before continuing lets make a small test if evryhing is working <br />
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

Change the optin 'Show column if' for first metric to  <br />
{% highlight metric %} 
WildMatch(vMetricShown, 'Balance', 'All') and WildMatch(vCompareMode, 'Values', 'All')
{% endhighlight %}
![varables](/img/20251108_0022/8.png) <br />

*Optional <br />
in load set the defalt variable state when opening dossie<br />
![varables](/img/20251108_0022/9.png) <br />
{% highlight metric %} 
SET vMetricShown = 'All';
SET vCompareMode = 'All';
{% endhighlight %}

Aditionaly  <br />
If you want to filter other atributes normaly don't forget to put '<font color='red'>* $</font>' in metric definition after alternative state. <br />
Sum({[Alt BASE scenario] <font color='red'>* $</font> } if(IsNull(MEAS_AMOUNT_01), 0, MEAS_AMOUNT_01))





