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
send screnshot

Connect to postgre sql
and run 
select * from platform_analytics_wh.lu_broker_list;  
select * from platform_analytics_wh.access_transactions order by tran_timestamp DESC limit 10; /*this shows if new data is loaded*/
send screshot or output

Info if this services are running
-Apache ZooKeeper (zookeeper)
-Apache Kafka (kafka)
-MicroStrategy Platform Analytics In-Memory Cache
-MicroStrategy Platform Analytics consumer
{% endhighlight %}


We can imagine data flow is like this INTELIGENCE > KAFKA > PLATFORM > POSTGRE > Platform Analytics project
During error testing stop services in the folowing order 
-MicroStrategy Platform Analytics consumer
-MicroStrategy Platform Analytics In-Memory Cache
-Apache Kafka (kafka)
-Apache ZooKeeper (zookeeper)

and run them like
-Apache ZooKeeper (zookeeper)
-Apache Kafka (kafka)
-MicroStrategy Platform Analytics In-Memory Cache
WAIT 10s
-MicroStrategy Platform Analytics consumer

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


### KAFKA > PLATFORM > POSTGRE
If you have problem that 'Apache Kafka' service is not running then the error is most likly here
Chek 'C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml'
if configuration of 'pgWarehouseDbConnection' is correct
you can try encrypting password with
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\bin\platform-analytics-encryptor.bat' (run cmd first in administrator mode)
and chek if it is the same


### INTELIGENCE > KAFKA
#### Data not send to kafka
If you get error in kafka logs
ERROR:(Local: Broker transport failure): <font color='red'>inteligence_server</font>:9092/0: Connect to ipv6#[<font color='red'>xxx</font>]:9092 failed: Unknown error (after 2013ms in state CONNECT)
2024-04-16 10:15:25.694+01:00[HOST:<font color='red'>inteligence_server</font>][PID:2932][THR:7080]Rdkafka event 

Then inteligence server is not sending data to kafaka you have to run 'Command manager'
{% highlight sql %} LIST ALL PROPERTIES FOR SERVER CONFIGURATION;{% endhighlight %}
and chek if bootstrap.servers:
have server with kafka. If not run 
{% highlight sql %} ALTER SERVER CONFIGURATION ENABLEMESSAGINGSERVICES TRUE CONFIGUREMESSAGINGSERVICES "bootstrap.servers:<font color='red'>Server_With_Kafka</font>:9092/BATCH.num.MESSAGES:5000/queue.buffering.max.ms:2000/MESSAGE.max.BYTES:1000000";{% endhighlight %}

#### Project not collecting data
In 'Command manager' run 
{% highlight sql %} LIST ALL PROPERTIES FOR PASTATISTICS IN PROJECT "your_project_name";{% endhighlight %}
![BassicStatistics](/img/20240420_0018/BassicStatistics.png)
If project dosen't have 'Basic Statistics' set to 'True' run
{% highlight sql %} ALTER PASTATISTICS BASICSTATS ENABLED DETAILEDREPJOBS FALSE DETAILEDDOCJOBS FALSE JOBSQL FALSE COLUMNSTABLES FALSE IN PROJECT "your_project_name";{% endhighlight %}


####
You can also chek if data is loded by running
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\bin\platform-analytics-health-check.bat' (first run cmd as admin)
insert id for project and id for report that you will run and change description for it.

### Last chek
After everything starts working run 'Licence Manager' and go to > Audit > Intelligence > Login (admin user and pass)' and press 'AUDIT'
In 'Developer' go to Administration > Configuration Managers > Events > (and triger) Load Metadata Object Telemetry

If platform survives loding this 2 than evrything will work.


### Change for how long data is stored 
Edit 'C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml'
Change
  viewCutoffRangeInDays: 666
  currentFactDataKeepDays: 999
First one shows how many days is loaded to cubes. 
Second one shows how long data is kept in database before deletion.


## Database for 'platform_analytics_wh' dosen't exist 
For this you will have to log in as POSTGRES user (find tutorial)
and run
{% highlight sql %} 
CREATE USER mstr_pa WITH ENCRYPTED PASSWORD 'ricola' NOSUPERUSER CREATEDB;
 
CREATE DATABASE platform_analytics_wh
  WITH 
  OWNER = mstr_pa
  ENCODING = 'UTF8'
  CONNECTION LIMIT = -1;

/*if previus gives you truble run this*/
--CREATE DATABASE platform_analytics_wh
--  WITH 
--  OWNER = mstr_pa
--  ENCODING = 'UTF8'
--  TEMPLATE = template0
--  CONNECTION LIMIT = -1;
 
ALTER ROLE mstr_pa SET search_path TO platform_analytics_wh
{% endhighlight %}

chek if 
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml' is correct 
then run 
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\bin\platform-analytics-consumer.bat' (first run cmd as admin)
if error chek
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml'
especially password that you can encrypt in
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\bin\platform-analytics-encryptor.bat'(first run cmd as admin)

