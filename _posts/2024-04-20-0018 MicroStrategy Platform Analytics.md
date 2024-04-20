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

This does not cover cluster setups.

If the folowing tutorial does not resolve your error prepare the folowing files for support<br />
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
<br />

We can imagine data flow is like this <br />
INTELIGENCE > KAFKA > PLATFORM > POSTGRE > Platform Analytics project<br />
During error testing stop services in the folowing order <br />
-MicroStrategy Platform Analytics consumer<br />
-MicroStrategy Platform Analytics In-Memory Cache<br />
-Apache Kafka (kafka)<br />
-Apache ZooKeeper (zookeeper)<br />

and run them like<br />
-Apache ZooKeeper (zookeeper)<br />
-Apache Kafka (kafka)<br />
-MicroStrategy Platform Analytics In-Memory Cache<br />
WAIT 10s<br />
-MicroStrategy Platform Analytics consumer<br />

### POSTGRE > Platform Analytics project
Lets start from the end if data is not shown in 'Platform Analytics project' even after you republished cubes. <br />
And there is data in POSTGRE if you run <br />
{% highlight sql %} select * from platform_analytics_wh.access_transactions order by tran_timestamp DESC limit 10; {% endhighlight %}

Then you have a problem with wrong ODBC<br />
- Chek ODBC Data Source Administration (64-bit) if you can connect to Postgre<br />
- Chek in 'Microstrategy Developer/ Administration / Configuration Managers / Database Instances / Platform Analytics' if connections are correct<br />

Info password for Postgre shud be writen in <br />
'Start / Microstrategy Tools / Install Password File' under 'mstr_pa'<br />
and default database name in 'platform_analytics_wh'<br />

#### Cube is too big
If you get error 'Unable to import your data since your remaning in-memory space od 100 MB ...'<br />
![Cube](/img/20240420_0018/Cube.png)<br />

Change it to bigger number since default is 100MB<br />
![Cube](/img/20240420_0018/Cube1.png)<br />

#### Cube can’t be created because there’s not too much disk space
Chek disk space you need at least 10gb of free space to refresh cube.<br />

### KAFKA > PLATFORM > POSTGRE
If you have problem that 'Apache Kafka' service is not running then the error is most likly here<br />
Chek 'C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml'<br />
if configuration of 'pgWarehouseDbConnection' is correct<br />
you can try encrypting password with<br />
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\bin\platform-analytics-encryptor.bat' (run cmd first in administrator mode)<br />
and chek if it is the same <br />


### INTELIGENCE > KAFKA
#### Data not send to kafka
If you get error in kafka logs<br />

{% highlight sql %}
ERROR:(Local: Broker transport failure): <font color='red'>inteligence_server</font>:9092/0: Connect to ipv6#[<font color='red'>xxx</font>]:9092 failed: Unknown error (after 2013ms in state CONNECT)
2024-04-16 10:15:25.694+01:00[HOST:<font color='red'>inteligence_server</font>][PID:2932][THR:7080]Rdkafka event 
{% endhighlight %}

Then inteligence server is not sending data to kafka. In 'Command manager' run<br />
{% highlight sql %} LIST ALL PROPERTIES FOR SERVER CONFIGURATION;{% endhighlight %}
and chek if bootstrap.servers:<br />
have server with kafka. <br />
If not run <br />
{% highlight sql %} ALTER SERVER CONFIGURATION ENABLEMESSAGINGSERVICES TRUE CONFIGUREMESSAGINGSERVICES "bootstrap.servers:<font color='red'>Server_With_Kafka</font>:9092/BATCH.num.MESSAGES:5000/queue.buffering.max.ms:2000/MESSAGE.max.BYTES:1000000";{% endhighlight %}

#### Project not collecting data
In 'Command manager' run <br />
{% highlight sql %} LIST ALL PROPERTIES FOR PASTATISTICS IN PROJECT "your_project_name";{% endhighlight %}<br />
![BassicStatistics](/img/20240420_0018/BassicStatistics.png) <br />
If project dosen't have 'Basic Statistics' set to 'True'. Then run<br />
{% highlight sql %} ALTER PASTATISTICS BASICSTATS ENABLED DETAILEDREPJOBS FALSE DETAILEDDOCJOBS FALSE JOBSQL FALSE COLUMNSTABLES FALSE IN PROJECT "your_project_name";{% endhighlight %}
Hint 'LIST ALL PROJECTS;' retuns names for all projects<br />

### You can also chek if data is loded by running<br />
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\bin\platform-analytics-health-check.bat' (first run cmd as admin)<br />
insert id for project and id for report that you will run and change description for it.<br />
![HC](/img/20240420_0018/HC.png)


### Last chek
After everything starts working run 'Licence Manager' and go to > Audit > Intelligence > Login (admin user and pass)' and press 'AUDIT'<br />
In 'Developer' go to Administration > Configuration Managers > Events > (and triger) Load Metadata Object Telemetry<br />

If platform survives loding this 2 than evrything will work.<br />

## Database for 'platform_analytics_wh' dosen't exist 
For this you will have to log in as POSTGRES user (find tutorial) <br />
and run <br />
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

chek if <br />
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml' is correct <br />
then run <br />
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\bin\platform-analytics-consumer.bat' (first run cmd as admin) <br />
run it like <br />
{% highlight sql %} 
platform-analytics-custom-install.bat -o
{% endhighlight %}
if error chek <br />
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml' <br />
especially password that you can encrypt in <br />
'C:\Program Files (x86)\MicroStrategy\Platform Analytics\bin\platform-analytics-encryptor.bat'(first run cmd as admin) <br />



# Extra
### Change for how long data is stored 
Edit 'C:\Program Files (x86)\MicroStrategy\Platform Analytics\conf\PAConsumerConfig.yaml' <br />
Change <br />
  viewCutoffRangeInDays: 666 <br />
  currentFactDataKeepDays: 999 <br />
First one shows how many days is loaded to cubes. <br />
Second one shows how long data is kept in database before deletion. <br />

### Not all user names are showing after refreshing cube
In 'Developer' go to Administration > Configuration Managers > Events > (and triger) Load Metadata Object Telemetry<br />
Wait few minutes and refresh cubes.

### Move data to another Repository
[repository_administration](https://www2.microstrategy.com/producthelp/Current/InstallConfig/en-us/Content/repository_administration.htm)

### Configuration 
During configuration set 'Platform Analytics Configuration' 
![Platform_Conection](/img/20240420_0018/platform_conection.png) <br />
it shud be full computer name and :9092

<p style="display: none;">If you have split inteligence and web setup then edit Topology during instalation
TO-DO
</p>


