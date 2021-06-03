---
layout: post
title: MicroStrategy - Change web timeout
tags:
- Microstrategy
- Web timeout
comments: true
---
Difficulty  ★★☆☆☆


# in progress



### To chek status of timeout
1) Go to <br />
http://localhost:8080/MicroStrategy/servlet/mstrWeb (Tomcat) <br />
or <br />
http://localhost/MicroStrategy/asp/ (IIS)  <br />
and log in as administrator<br />
3) Open any project<br />
4) On the right side click (user) 'Administrator' and select 'Preferences'<br />

![ADMIN_Preferecnes](/img/20210603_0006/ADMIN_Preferecnes.png)

4) Select 'Project Defaults' <br />
![Project_Default](/img/20210603_0006/Project_Default.png)

5) Under 'General' find 'Session timeout warning'<br />
![Seassion_timeot](/img/20210603_0006/Seassion_timeot.png)

The first number is inteligence server seassion timeout and is controled by MicroStrategy<br />
The second is HTTP seassion controled by Tomcat/IIS<br />


### Changing inteligence server seassion timeout
1) Log into Developer as 'Administrator'<br />
2) Right clik on 'Project source'<br />
3) Select 'Configure MicroStrategy Intelligence Server'<br />
![Developer_Configure_Microstrategy_Intelligence_Server](/img/20210603_0006/Developer_Configure_Microstrategy_Intelligence_Server.png)<br />
5) Under 'Governing Rules/Default/Genreal' you will find <br />
{% highlight bash %}
User session idle time (sec):     
Web user session idle time (sec):
{% endhighlight %}
![Microstrategy_Intelligence_Server_Configuration_Session_Idle](/img/20210603_0006/Microstrategy_Intelligence_Server_Configuration_Session_Idle.png)<br /> <br />
More info on what they mean [KB220558](https://community.microstrategy.com/s/article/KB220558-How-do-user-session-idle-timeouts-work-in-MicroStrategy?language=en_US)  <br />


Input (time how long users can be loged in) values in seconds.<br />
1h   --> 3600 <br />
1,5h --> 5400 <br />
2h   --> 7200 <br />

#### Restart intelligence server - Windows
Start/Microstrategy Tools/Service Manager <br />
![Service Manager](/img/20210603_0006/Service_Manager.png)

and restart <br />
![Intelligence_Restart](/img/20210603_0006/Intelligence_Restart.png)

to see changes you might also need to restart tomcat/IIS but it is not needed at this time.

#### Restart intelligence server - Linux
go to 
{% highlight bash %}
cd <MicroStrategy Home>/bin
{% endhighlight %}
  
Enter
{% highlight bash %}
mstrctl -s IntelligenceServer stop
{% endhighlight %}

{% highlight bash %}
mstrctl -s IntelligenceServer gs
{% endhighlight %}

Wait until status is 'stoped' and then enter
{% highlight bash %}
mstrctl -s IntelligenceServer start
{% endhighlight %}

To see changes you might also need to restart tomcat/IIS but it is not needed at this time.

### Change HTTP seassion timeout
#### Tomcat [KB12966](https://community.microstrategy.com/s/article/KB12966-How-to-configure-the-web-session-timeout-set-on-JSP)
Go to <br />
C:\Program Files (x86)\Common Files\MicroStrategy\Tomcat\apache-tomcat-X.X.XX\conf <br />
and open 'web.xml' <br />
![Tomcat_web](/img/20210603_0006/Tomcat_web.png)  

in it find 
{% highlight bash %}
    <session-config>
        <session-timeout>30</session-timeout>
    </session-config>
{% endhighlight %}
  
and chenge setting to <br />
1h   --> 60 <br />
1,5h --> 90 <br />
2h   --> 120 <br />  
  
##### Restart Tomcat - Windows 
Start services <br />
![Services](/img/20210603_0006/Services.png) <br />    

Find Tomcat and select restart<br />
![Tomcat_Restart](/img/20210603_0006/Tomcat_Restart.png) 

##### Restart Tomcat - Linux 
{% highlight bash %}
cd <Tomcat Root>/bin/
./shutdown.sh
{% endhighlight %}
  
{% highlight bash %}
./startup.sh
{% endhighlight %}
  
and you are done <br />
![Tomcat_Final](/img/20210603_0006/Final.png)
  
#### IIS
Open IIS go to Sites/Default Web Site/ MicroStrategy and find 'Session State'
![Session_State](/img/20210603_0006/Session_State.png)

and change setting for 'Time-out (in minutes)' <br />
1h   --> 60 <br />
1,5h --> 90 <br />
2h   --> 120 <br />
![Session_State_2](/img/20210603_0006/Session_State_2.png)

Restart IIS <br />
![IIS_Stop](/img/20210603_0006/IIS_Stop.png)

and you are done <br />
![IIS_Final](/img/20210603_0006/Final.png)
  
### Aditional read  
[KB441269 What are the timeout settings controlled by the Web server?](https://community.microstrategy.com/s/article/What-are-the-timeout-settings-controlled-by-the-Web-Server?language=en_US)
 
[KB12867 How timeout settings in MicroStrategy Web and the MicroStrategy Intelligence Server affect users in MicroStrategy Web](https://community.microstrategy.com/s/article/KB12867-How-timeout-settings-in-MicroStrategy-Web-and-the)
