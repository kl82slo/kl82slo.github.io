---
layout: post
title: MicroStrategy The Craziness that is Platform Analytics
tags:
- Microstrategy
- Platform Analytics
comments: true
---
Dificulty ★☆☆☆☆

# IN PROGRES

If the folowing tutorial does not resolve your error prepare the folowing files
{% highlight sql %} 
C:\Program Files (x86)\MicroStrategy\Intelligence Server\KFKProducerError.log
C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml
C:\Program Files (x86)\MicroStrategy\Platform Analytics\log\platform-analytics-consumer.log
C:\Program Files (x86)\Common Files\MicroStrategy\Log\DssError.log
C:\Program Files (x86)\MicroStrategy\Messaging Services\Kafka\kafka_xxx\config	(whole folder)
C:\Program Files (x86)\MicroStrategy\Messaging Services\Kafka\kafka_xxx\logs	(whole folder)


in command manager run
LIST ALL PROPERTIES FOR SERVER CONFIGURATION;
find bootstrap.servers:
send screshot

Connect to postgre sql
and run 
select * from platform_analytics_wh.lu_broker_list;
select * from platform_analytics_wh.access_transactions order by tran_timestamp DESC limit 10;
send screshot or output

Info if this services are running
-Apache ZooKeeper (zookeeper)
-Apache Kafka (kafka)
-MicroStrategy Platform Analytics In-Memory Cache
-MicroStrategy Platform Analytics consumer
{% endhighlight %}


We can imagine data flow is like this INTELIGENCE > KAFKA > PLATOFRM > POSTGRE > Platform Analytics project

### POSTGRE > Platform Analytics project
Lets start from the end in data is not shown in 'Platform Analytics project' even after you republih cubes. 
And there is data in POSTGRE if you run 
{% highlight sql %} select * from platform_analytics_wh.access_transactions order by tran_timestamp DESC limit 10; {% endhighlight %}

Then you have a problem with wrong ODBC
- Chek ODBC Data Source Administration (64-bit) if you can connect to Postgre
- Chek in 'Microstrategy Developer/ Administration / Configuration Managers / Database Instances / Platform Analytics' if connections are correct

Info password for Postgre shud be writen in 
'Start / Microstrategy Tools / Install Password File' under 'mstr_pa'
and default database name in 'platform_analytics_wh'






