---
layout: post
title: MicroStrategy - Cancel subscription if condition not met
tags:
- Microstrategy
- Distribution service
comments: true
---
Dificulty ★☆☆☆☆

## How to cancel execution subscription if condition not met

In this example we will chek how to cancel excecution of distribution service subscription because data is not yet ready and want to keep previus export of files.

# Creating table for lookup

{% highlight bash %}
CREATE TABLE [dbo].[Does_it_execute](
	name [nvarchar](50) NOT NULL,
	run [int] NOT NULL
) ON [PRIMARY]
{% endhighlight %}

use ETL to updete this table with 
1 for excecution
2 for stoping excecution


and for this example

{% highlight bash %}
insert into [Does_it_execute]
VALUES  ('Report_001', 1)
{% endhighlight %}


# microstrategy - show error
if we want to show error in logs add this select statement to pre statement

{% highlight bash %}
SELECT run/run as st
  FROM [NIKO].[dbo].[Does_it_execute]
  where name = 'Report_001'
{% endhighlight %}

![VLDB](/img/20221002_0012/Vldb_properties.png) 

![pre statement](/img/20221002_0012/Pre_Statement.png) 

and if we run report it will generate

but if we change the status

{% highlight bash %}
update [Does_it_execute]
set  run = 0
where name = 'Report_001'
{% endhighlight %}

report will faill

![Error Zerro](/img/20221002_0012/Error_Zerro.png) 



# microstrategy - don't show errors
if you don't want errors to be shown register atribute and metric

![Register](/img/20221002_0012/Register.png) 

and add new metric as filter

![Report limit](/img/20221002_0012/Report_limit.png) 

now in case status is 0 we will get

![Report limit](/img/20221002_0012/zerro_errors.png) 





