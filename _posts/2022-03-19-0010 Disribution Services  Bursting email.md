---
layout: post
title: MicroStrategy - Distribution Services - Bursting email
tags:
- MicroStrategy
- Distribution Services
- Email
comments: true
---
Dificulty ★★☆☆☆

## Intro
How to use table as source for emails and filters whithin distribution service.

## Source
For this example I will be using the following report  <br /> 
![BASIC_DATA_01](/img/20220319_0010/0_Drzave.png)

And for easier understanding of the following here are the sources for it   <br /> 
![BASIC_DATA_01](/img/20220319_0010/1_F_Countires.png)

![BASIC_DATA_01](/img/20220319_0010/2_D_Countries.png)

## Crate table
The first thing you need to do is create a new table with 
- emails
- filter (Country colum)
- and (not necessarily) a colum to seperate difrent requirements (Report colum)

{% highlight sql %}  
CREATE TABLE [dbo].[Email_Bursting](
	  Country	[smallint]  NULL
	, Email		[varchar](70) NULL
	, Report	[varchar](20) NULL
) 

{% endhighlight %}

input some data   
{% highlight sql %}  
INSERT INTO [dbo].[Email_Bursting]
           ([Country]
           ,[Email]
           ,[Report])
     VALUES
          (705,'demo_email1.gmail.com','Report 1')
	, (191,'demo_email1.gmail.com','Report 1')
	, (642,'demo_email1.gmail.com','Report 1')
	, (705,'demo_email2.gmail.com','Report 1')
	, (300,'demo_email3.gmail.com','Report 1')
	, (724,'demo_email3.gmail.com','Report 2')
	
{% endhighlight %}

![BASIC_DATA_01](/img/20220319_0010/3_Email.png)

## Registring new table in MicroStrategy
When you import the new table don't forget to set Email as 'Form format' type 'Email'.
![BASIC_DATA_01](/img/20220319_0010/4.png)

and set the Parent/Child correctly
![BASIC_DATA_01](/img/20220319_0010/6.png)

## Limiting reports
If you have different requirements don't forget to limit them on report
![BASIC_DATA_01](/img/20220319_0010/5.png)

Result for first email   <br /> 
![BASIC_DATA_01](/img/20220319_0010/7_email_1.png)

Result for second email   <br /> 
![BASIC_DATA_01](/img/20220319_0010/8_email_2.png)

## Subscription
Once ready go to subscription and set 'Burst' to email.
![BASIC_DATA_01](/img/20220319_0010/11_DC_Burst.png)

![BASIC_DATA_01](/img/20220319_0010/12_DB_Burst_Email.png)

And that is it.

### Additional sources
[KB221199](https://community.microstrategy.com/s/article/KB221199-New-feature-in-MicroStrategy-10-Bursting-to-Email?language=en_US)
