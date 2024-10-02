---
layout: post
title: How to run BAT file in MicroStrategy
tags:
- Microstrategy
- SQL
comments: true
---
Dificulty ★★★☆☆


## Prologue
Occasionally, you may want to run a BAT file directly from MicroStrategy.<br />
In this guide, we will discuss how to execute it from an SQL Server.<br />
While this is a viable option, keep in mind that it poses a security risk.<br />



## JAVA (optional)
If you created the BAT file in Talend, you need to perform an additional step. <br />

On the SQL Server, download (OpenJDK)[https://www.openlogic.com/openjdk-downloads]
, and extract it to the desired location (in this tutorial it was C:\Program Files\openlogic-openjdk).<br />


Add system vatiable<br />
JAVA_JOME<br />
C:\Program Files\openlogic-openjdk<br />
![JAVA_HOME](/img/20240726_0020/java_home.png)<br />

In Enviroment Varables add path to Java bin location<br />
![JAVA path](/img/20240726_0020/java_path.png)<br />

Test Java in the Command Prompt (CMD) by running: <br />
Java --version<br />
![JAVA test](/img/20240726_0020/java_test.png)<br />

If you get a version output, everything was set up correctly.<br />

Now, restart the SQL Server service.<br />
![SQL_Restart](/img/20240726_0020/SQL_restart.png)<br />

## Copy BAT file to SQL server

Copy the bat file to the server<br />
In my case, I copied the entire Talend job to the following location:<br />
C:\ETL_job\Get_Data_2023_0.1<br />
<br />
so the BAT file is in location<br />
C:\ETL_job\Get_Data_2023_0.1\Get_Data_2023\Get_Data_2023_run.bat<br />
<br />
The files I will be processing are located at: <br />
C:\ETL_STEAM<br />

## Setting Folder Permissions on SQL Server
On SQL server chek the name of user that is running 'SQL server'<br />
![SQL_User](/img/20240726_0020/SQL_restart.png)<br />

Grant the necessary permissions to the folder that will be used to process files.<br />
![SQL_Permition](/img/20240726_0020/SQL_Permition.png)<br />

Set permissions for all subfolders as well.<br />
![Windows_Permition](/img/20240726_0020/Windows_permition.png)<br />


## Create a procedure to run the BAT file

To create a stored procedure that runs the BAT file, use the following SQL code:<br />
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


After creating the procedure, you can test it by running:<br />
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
You can use a standard report, but I prefer Freeform SQL,<br />
where I can write custom text when the procedure was executed.<br />
![freeform](/img/20240726_0020/freeform.png)<br />

and add VLDB option to run procedure from SQL<br />
![VLDB](/img/20240726_0020/VLDB.png)<br />

Once this setup is complete, the user simply needs to run the report.
![MSTR_Web](/img/20240726_0020/MSTR_Web.png)<br />

![MSTR_Running](/img/20240726_0020/MSTR_bat_run.png)<br />

After the procedure completes, the user will either see the results or experience a timeout<br />
![MSTR_Web_bat_run](/img/20240726_0020/Mstr_web_bat.png)<br />

