# in progress




### To chek status of timeout
1) Go to http://localhost:8080/MicroStrategy/asp/Main.aspx and log in as administrator
2) Open any project
3) On the right side click 'Administrator' and select 'Preferences'

![ADMIN_Preferecnes](/img/2021-03-03-0006/ADMIN_Preferecnes1.gif)

4) Select 'Project Defaults' 
![Project_Default](/img/2021-03-03-0006/Project_Default.gif)

5) Under 'General' find 'Session timeout warning'
![Seassion_timeot](/img/2021-03-03-0006/Seassion_timeot.gif)

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
