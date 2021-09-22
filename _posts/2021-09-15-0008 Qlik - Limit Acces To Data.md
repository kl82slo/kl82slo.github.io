---
layout: post
title: Qlik - Limit Acces To Data (in progress)
tags:
- Qlik
- Section Acces
- Permissions
comments: true
---
Dificulty ★★★☆☆

# IN PROGRESS



## Why do it?
In some cases you need to limit what users can see in certan qlik files. <br />
This solution means that users do not need to reload data. <br />
And will only see the data that he is allowed to see.<br />
But in case you have huge quantity of rows it will affect the performance of opening qlik files.<br />


## Lets create some demo data
In data load create some data<br />
![BASIC_DATA](/img/20210915_0008/BASIC_DATA.png){:height="90%" width="90%"} 

<br />
{% highlight sql %}
[Random_Data]:
SELECT '001'  as Connecting_Column
      ,'piko' as Shown_Data
      union
SELECT '002'  as Connecting_Column
      ,'miko' as Shown_Data     
;
{% endhighlight %}

<br />
And show it in Qlik  
In the first grid lets add 'Connecting_Column' and 'Shown_Data' <br />
In a seperate grid add function OSuser() <br />
<br />
![BASIC_DATA_01](/img/20210915_0008/BASIC_DATA_01.png){:height="75%" width="75%"}


  
{% highlight sql %}
=OSuser()
{% endhighlight %}

In the second grind you will find your user and group.  
  
## First limitation
  
{% highlight sql %}  
Section Access;
[Security]:
LOAD * inline [
ACCESS, USERID, REDUCTION
ADMIN, QLIK-TEST\qliksvc ,  1
];
Section Application; 
{% endhighlight %}

This part creates hidden table that is used in Qlik and recognizes logged in user with user in this table.<br />
In ACCESS input USER or ADMIN or * <br />
In USERID input user as 'UserDirectory' + '/' + 'UserId' or use * <br />
REDUCTION for this one you can use any name but you will have to connect to it later. It specifies in which group of filters user belongs.<br />

If you want to add multiple user just add lines betwen [] like
{% highlight sql %}
Section Access;
[Security]:
LOAD * inline [
ACCESS, USERID, REDUCTION
ADMIN, QLIK-TEST\qliksvc ,  1
ADMIN, QLIK-TEST\foo ,  1
USER, QLIK-TEST\bar ,  2
USER, QLIK-TEST\baz ,  3
*, QLIK-TEST\quux ,  3
];
Section Application; 
{% endhighlight %}

<br />

For REDUCTION - you can use difrent name
{% highlight sql %}  
REDUCTION:
LOAD * inline [
REDUCTION , Connecting_Column
1 , 001
1 , 002
];
{% endhighlight %}

It just represents groups of filters like in current example user in group 1 can see 001 and 002 in Connecting Column. <br />
You will see examples as we proceed.

<br />











  
  
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
<br />
And we will get all current data <br /> 
![D2_ALL](/img/20210915_0008/D2_ALL.png){:height="75%" width="75%"}
  

  <br />
  
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
  

![D3_Limit](/img/20210915_0008/D3_Limit.png){:height="75%" width="75%"}  
  
## Lets add more data
  
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

![D4_New_Limit](/img/20210915_0008/D4_New_Limit.png){:height="75%" width="75%"}   

## Change ACCES to *  
  
{% highlight bash %}
LIB CONNECT TO 'Microsoft_SQL_Server_192.xxx.xxx.xxx';


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
  
![D4_New_Limit](/img/20210915_0008/D4_New_Limit.png){:height="75%" width="75%"}     
  
## Move limitation to Database
![D4_New_Limit](/img/20210915_0008/SQL1.png){:height="50%" width="50%"} <br />
![D4_New_Limit](/img/20210915_0008/SQL2.png){:height="50%" width="50%"} <br />

{% highlight bash %}
LIB CONNECT TO 'Microsoft_SQL_Server_192.xxx.xxx.xxx';

And change select to database
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
