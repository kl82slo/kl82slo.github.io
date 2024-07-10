---
layout: post
title: MicroStrategy Quoting Behavior
tags:
- Microstrategy
- SQL
comments: true
---
Dificulty ★☆☆☆☆

If you have an issue that all your SQL genereted by microstrategy have every colum and table in "

{% highlight sql %} 
select "a11"."ATTRIBUTE"  COLUMN_ID,
 sum("a11"."METIRC")  WJXBFS1
from "TABLE_NAME" a11
group by "a11"."ATTRIBUTE"
{% endhighlight %}
<br />

and the VLDB setting look correct<br />
![Cube](/img/20240704_0019/VLDB.jfif)<br />
[KB30665](https://community.microstrategy.com/s/article/KB30665-How-to-change-the-syntax-with-column-names-and-table)<br />

then you can try this setting.

![Cube](/img/20240704_0019/Project_VLDB.png)<br />

![Cube](/img/20240704_0019/Quoting.png)<br />
