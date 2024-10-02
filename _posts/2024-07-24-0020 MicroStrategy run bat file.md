---
layout: post
title: How to run BAT file in MicroStrategy
tags:
- Microstrategy
- SQL
comments: true
---
Dificulty ★★★☆☆


## Prelog
Once in a while you want an option to run BAT file directly from MicroStrategy. <br />
The option that will be discused here is to run it from SQL server.<br />
While that is an option remember that it is a scurity risk.<br />


## JAVA (optional)
If you created BAT file in TALEND then you need to do this extra step. <br />

On SQL server download <br />
(JAVA_OpenJDK)[https://www.openlogic.com/openjdk-downloads]

extract it to<br />
![Java_Download](/img/20240726_0020/java.png)<br />

Add system vatiable<br />
JAVA_JOME<br />
C:\Program Files\openlogic-openjdk<br />
![JAVA_HOME](/img/20240726_0020/java_home.png)<br />

In Enviroment Varables add path to Java bin location<br />
![JAVA path](/img/20240726_0020/java_path.png)<br />

Test JAVA in CMD <br />
Java --version<br />
![JAVA test](/img/20240726_0020/java_test.png)<br />

if you get a result than you did it ok.

Now yust restart service for SQL Server.
![SQL_Restart](/img/20240726_0020/SQL_restart.png)<br />

## Copy BAT file to SQL server

Copy the bat file to the server<br />
in my case i copied whole Talend yob to <br />
C:\ETL_job\Get_Data_2023_0.1<br />
<br />
so the bat file is in location<br />
C:\ETL_job\Get_Data_2023_0.1\Get_Data_2023\Get_Data_2023_run.bat<br />
<br />
the files that I will be procesing are in location <br />
C:\ETL_STEAM<br />

## Setting folder permition on SQL server
On SQL server chek the name of user that is running 'SQL server'<br />
![SQL_User](/img/20240726_0020/SQL_restart.png)<br />

Then add permiton to the folder that will be used to process files<br />
![SQL_Permition](/img/20240726_0020/SQL_Permition.png)<br />

Than set permitions for subfolders<br />
![Windows_Permition](/img/20240726_0020/Windows_permition.png)<br />


## Create a procedure to run the BAT file

Create procedure to run bat file<br />
{% highlight sql %} 
CREATE PROCEDURE kl_run_steam_update
AS
BEGIN
-- Enable xp_cmdshell start
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
-- Enable xp_cmdshell end

/*Execute the batch file*/
EXEC xp_cmdshell 'C:\ETL_job\Get_Data_2023_0.1\Get_Data_2023\Get_Data_2023_run.bat';

-- Disable xp_cmdshell start
EXEC sp_configure 'xp_cmdshell', 0;
RECONFIGURE;
-- Disable xp_cmdshell end
END
{% endhighlight %}
<br />


and test it<br />
{% highlight sql %} 
exec [dbo].kl_run_steam_update
{% endhighlight %}
<br />

if you get an error<br />
activate the addvanced options and try aggain<br />
![SQL Error](/img/20240726_0020/Error.png)<br />

{% highlight sql %} 
sp_configure 'show advanced options', '1'
RECONFIGURE
{% endhighlight %}
<br />


## Microstrategy
You can use normal report but I preffer freeform<br />
Where I write costum text when procedure was run<br />
![freeform](/img/20240726_0020/freeform.png)<br />

and add VLDB option to run procedure from SQL<br />
![VLDB](/img/20240726_0020/VLDB.png)<br />

Now to activate it user just has to run a report
![MSTR_Web](/img/20240726_0020/MSTR_Web.png)<br />

![MSTR_Running](/img/20240726_0020/MSTR_bat_run.png)<br />

And after it is done user will get result (or a timeot) <br />
![MSTR_Web_bat_run](/img/20240726_0020/Mstr_web_bat.png)<br />

