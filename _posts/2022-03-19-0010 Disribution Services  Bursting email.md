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

# In progress

## Intro
If we want to use table as source for emails and filters 

TODO

## Source
For this exaple I will be using the folowing report

![BASIC_DATA_01](/img/2022-03-19-0010_0010/0_Drzave.png)

And for easier understanding of the folowing here are the sources for it

![BASIC_DATA_01](/img/2022-03-19-0010_0010/1_F_Countires.png)

![BASIC_DATA_01](/img/2022-03-19-0010_0010/2_D_Countries.png)

## Crate table
The first thing you need to do is create a new table with 
- emails
- filter (Country colum)
- and (not necessarily) a colum to seperate difrent requirements (Report colum)

{% highlight sql %}  
CREATE TABLE [dbo].[Email_Bursting](
	  Country	[smallint]  NULL
	, Email		[nchar](70) NULL
	, Report	[nchar](20) NULL
) 
{% endhighlight %}

input some data
![BASIC_DATA_01](/img/2022-03-19-0010_0010/3_Email.png)

## Registring new table in MicroStrategy
When you import the new table don't forget to set Email as format type 'Email'.
![BASIC_DATA_01](/img/2022-03-19-0010_0010/4.png)

and set the Parent/Chiled corectly
![BASIC_DATA_01](/img/2022-03-19-0010_0010/6.png)

## Limiting reports
If you have difrent reqirements don't forget to limit them on report
![BASIC_DATA_01](/img/2022-03-19-0010_0010/5.png)

Result for first email
![BASIC_DATA_01](/img/2022-03-19-0010_0010/7_email_1.png)

Result for second email
![BASIC_DATA_01](/img/2022-03-19-0010_0010/8_email_2.png)

## Subscription
Once ready go to subscription and set

![BASIC_DATA_01](/img/2022-03-19-0010_0010/11_DC_Burst.png)

![BASIC_DATA_01](/img/2022-03-19-0010_0010/12_DB_Burst_Email.png)

And that is it.

### Additional sources
[KB221199](https://community.microstrategy.com/s/article/KB221199-New-feature-in-MicroStrategy-10-Bursting-to-Email?language=en_US)