---
layout: post
title: Qlik - Limit Acces To Data
tags:
- Qlik
- Section Acces
- Permissions
comments: true
---
Dificulty ★☆☆☆☆

# IN PROGRESS



## Why do it?
<TO-DO>
  
 
## Lets create some basic data
![BASIC_DATA](/img/20210915_0008/BASIC_DATA.png)
  
{% highlight bash %}
    [Random_Data]:
    SELECT '001'  as Connecting_Column
          ,'piko' as Shown_Data
          union
     SELECT '002' as Connecting_Column
          ,'miko' as Shown_Data     
    ;
{% endhighlight %}
  
  
  
  
![BASIC_DATA_01](/img/20210915_0008/BASIC_DATA_01.png)

In the first grid lets add 'Connecting_Column' and 'Shown_Data'
  
In a seperate grid add function OSuser()
  
{% highlight bash %}
    =OSuser()
{% endhighlight %}

In the second grind you will find your user and group.  
  
## First limitation

  
  
  
