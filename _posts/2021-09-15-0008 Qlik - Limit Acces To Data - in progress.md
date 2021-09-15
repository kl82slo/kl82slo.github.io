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
TO-DO
  
 
## Lets create some basic data
![BASIC_DATA](/img/20210915_0008/BASIC_DATA.png)

{% highlight sql %}
[Random_Data]:
SELECT '001'  as Connecting_Column
      ,'piko' as Shown_Data
      union
SELECT '002' as Connecting_Column
      ,'miko' as Shown_Data     
;
{% endhighlight %}
  
  
  
  
![BASIC_DATA_01](/img/20210915_0008/BASIC_DATA_01.png)

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

And we will get all current data
![D2_ALL](/img/20210915_0008/D2_ALL.png)  
  
Now if we change REDUCTION table

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
  

![D3_Limit](/img/20210915_0008/D3_Limit.png)  
  
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

![D4_New_Limit](/img/20210915_0008/D4_New_Limit.png)   

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
  
![D4_New_Limit](/img/20210915_0008/D4_New_Limit.png)     
  
## Move limitation to Database
![D4_New_Limit](/img/20210915_0008/SQL1.png)  
![D4_New_Limit](/img/20210915_0008/SQL2.png)    
  
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

{% endhighlight %}
![Post_SQL](/img/20210915_0008/Post_SQL.png)     
  
And if we change data to
![Post_SQL1](/img/20210915_0008/Post_SQL1.png)       

we will get  
![Post_SQL2](/img/20210915_0008/Post_SQL2.png)    
