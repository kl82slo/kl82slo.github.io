---
layout: post
title: Qlik - Limit Acces To Data
tags:
- Qlik
- Section Acces
- Permissions
comments: true
---
Dificulty ★★★☆☆

# IN PROGRESS



## Why do it?

  
 
## Lets create some basic data
![BASIC_DATA](/img/20210915_0008/BASIC_DATA.png){:height="90%" width="90%"} 

{% highlight sql %}
[Random_Data]:
SELECT '001'  as Connecting_Column
      ,'piko' as Shown_Data
      union
SELECT '002' as Connecting_Column
      ,'miko' as Shown_Data     
;
{% endhighlight %}
  
  
  
  
![BASIC_DATA_01](/img/20210915_0008/BASIC_DATA_01.png){:height="50%" width="50%"}

In the first grid lets add 'Connecting_Column' and 'Shown_Data'
  
In a seperate grid add function OSuser()
  
{% highlight sql %}
=OSuser()
{% endhighlight %}

In the second grind you will find your user and group.  
  
## First limitation
  
{% highlight sql %}
LIB CONNECT TO 'Microsoft_SQL_Server_192.xxx.xxx.xxx';


Section Access;
[Security]:
LOAD * inline [
ACCESS, USERID, REDUCTION
ADMIN, QLIK-TEST\qliksvc ,  1
];
Section Application;


REDUCTION:
LOAD * inline [
REDUCTION , Connecting_Column
1 , 001
1 , 002
];


[Random_Data]:
SELECT '001'  as Connecting_Column
      ,'piko' as Shown_Data
      union
SELECT '002' as Connecting_Column
      ,'miko' as Shown_Data     
;
{% endhighlight %}

And we will get all current data <br />
![D2_ALL](/img/20210915_0008/D2_ALL.png){:height="50%" width="50%"}
  
Now if we change REDUCTION table <br />

{% highlight sql %}
LIB CONNECT TO 'Microsoft_SQL_Server_192.xxx.xxx.xxx';


Section Access;
[Security]:
LOAD * inline [
ACCESS, USERID, REDUCTION
ADMIN, QLIK-TEST\qliksvc ,  1
];
Section Application;


REDUCTION:
LOAD * inline [
REDUCTION , Connecting_Column
1 , 001
];


[Random_Data]:
SELECT '001'  as Connecting_Column
      ,'piko' as Shown_Data
      union
SELECT '002' as Connecting_Column
      ,'miko' as Shown_Data     
;
{% endhighlight %}
  

![D3_Limit](/img/20210915_0008/D3_Limit.png){:height="50%" width="50%"}  
  
## Lets add more data
  
{% highlight sql %}
LIB CONNECT TO 'Microsoft_SQL_Server_192.xxx.xxx.xxx (qlik-test_qliksvc)';


Section Access;
[Security]:
LOAD * inline [
ACCESS, USERID, REDUCTION
ADMIN, QLIK-TEST\qliksvc ,  1
];
Section Application;


REDUCTION:
LOAD * inline [
REDUCTION , Connecting_Column
1 , 001
1 , 003
];


[Random_Data]:
SELECT '001'  as Connecting_Column
      ,'piko' as Shown_Data
union
SELECT '002' as Connecting_Column
      ,'miko' as Shown_Data     
union
SELECT '003' as Connecting_Column
      ,'zingo' as Shown_Data    
;
{% endhighlight %}  

![D4_New_Limit](/img/20210915_0008/D4_New_Limit.png){:height="50%" width="50%"}   

## Change ACCES to *  

TO-DO - why
  
{% highlight bash %}
LIB CONNECT TO 'Microsoft_SQL_Server_192.xxx.xxx.xxx (qlik-test_qliksvc)';


Section Access;
[Security]:
LOAD * inline [
ACCESS, USERID, REDUCTION
*, QLIK-TEST\qliksvc ,  1
];
Section Application;


REDUCTION:
LOAD * inline [
REDUCTION , Connecting_Column
1 , 001
1 , 003
];


[Random_Data]:
SELECT '001'  as Connecting_Column
      ,'piko' as Shown_Data
union
SELECT '002' as Connecting_Column
      ,'miko' as Shown_Data     
union
SELECT '003' as Connecting_Column
      ,'zingo' as Shown_Data    
;

{% endhighlight %}  
  
![D4_New_Limit](/img/20210915_0008/D4_New_Limit.png){:height="50%" width="50%"}     
  
## Move limitation to Database
![D4_New_Limit](/img/20210915_0008/SQL1.png){:height="75%" width="75%"}
![D4_New_Limit](/img/20210915_0008/SQL2.png){:height="75%" width="75%"}

{% highlight bash %}
LIB CONNECT TO 'Microsoft_SQL_Server_192.xxx.xxx.xxx';


Section Access;
[Security]:
LOAD ACCESS, USERID, REDUCTION;
SELECT [ACCESS]
      ,upper([USERID]) as USERID
      ,[REDUCTION]
FROM [dbo].[A_SECURITY];
Section Application;


REDUCTION:
LOAD REDUCTION, Connecting_Column;
SELECT [REDUCTION]
      ,Connecting_Column
      from dbo.A_REDUCTION;



[Random_Data]:
SELECT '001'  as Connecting_Column
      ,'piko' as Shown_Data
union
SELECT '002'  as Connecting_Column
      ,'miko' as Shown_Data     
union
SELECT '003'   as Connecting_Column
      ,'zingo' as Shown_Data    
;

{% endhighlight %} <br />
![Post_SQL](/img/20210915_0008/Post_SQL.png){:height="75%" width="75%"}     
  
And if we change data to <br />
![Post_SQL1](/img/20210915_0008/Post_SQL1.png){:height="50%" width="50%"}       

we will get  <br />
![Post_SQL2](/img/20210915_0008/Post_SQL2.png){:height="75%" width="75%"}    
