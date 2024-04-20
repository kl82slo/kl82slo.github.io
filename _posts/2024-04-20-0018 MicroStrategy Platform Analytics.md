---
layout: post
title: MicroStrategy The Craziness that is Platform Analytics
tags:
- Microstrategy
- Platform Analytics
comments: true
---
Dificulty ★☆☆☆☆

#In PROGRES

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
{% endhighlight %}

