# in progress




### To chek status of timeout
1) Go to http://localhost:8080/MicroStrategy/asp/Main.aspx and log in as administrator
2) Open any project
3) On the right side click 'Administrator' and select 'Preferences'

![ADMIN_Preferecnes](/img/2021-06-03-0006/ADMIN_Preferecnes1.png)

4) Select 'Project Defaults' 
![Project_Default](/img/2021-06-03-0006/Project_Default.png)

5) Under 'General' find 'Session timeout warning'
![Seassion_timeot](/img/2021-06-03-0006/Seassion_timeot.png)

The first number is inteligence server seassion timeout
The second is HTTP seassion controled by Tomcat/IIS


### Changing inteligence server seassion timeout
1) Log into Developer as 'Administrator'
2) Right clik on 'Project source'
3) Select 'Configure MicroStrategy Intelligence Server'
4) Under 'Governing Rules/Default/Genreal' you will find 
{% highlight bash %}
User session idle time (sec):     
Web user session idle time (sec):
{% endhighlight %}
More info on what they mean [KB220558](https://community.microstrategy.com/s/article/KB220558-How-do-user-session-idle-timeouts-work-in-MicroStrategy?language=en_US)
![Developer_Configure_Microstrategy_Intelligence_Server](/img/2021-06-03-0006/Developer_Configure_Microstrategy_Intelligence_Server.png)

Input (time how long users can be loged in) values in seconds.
1h   --> 3600 
1,5h --> 5400
2h   --> 7200

Restart intelligence server







