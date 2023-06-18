---
layout: post
title: MicroStrategy calculate date from prompt
tags:
- Microstrategy
- Prompt
- Date calculation
comments: true
---
Dificulty ★★★☆☆

### INTRO
This case shows example how to caclucate dates from database with date from prompt without seperate date table.

#### First lets create some basic data

{% highlight sql %}
CREATE VIEW F_Data_V AS
SELECT CAST('2000-01-01' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue UNION
SELECT CAST('2023-01-01' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue UNION
SELECT CAST('2023-01-02' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue UNION
SELECT CAST('2023-01-03' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue UNION
SELECT CAST('2023-01-04' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue UNION
SELECT CAST('2023-01-05' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue UNION
SELECT CAST('2022-12-01' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue UNION
SELECT CAST('2022-12-31' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue UNION
SELECT CAST('2022-12-30' AS DATE) AS Day_Date , CONVERT(DECIMAL(10,2),RAND(CHECKSUM(NEWID())) * 10000) AS Revenue 
{% endhighlight %}



![Basic data](/img/20230106_0013/0_basic_data.png)



#### Then register atribute and metric normaly
![Register normally](/img/20230106_0013/1_Register_Normally.png)


#### Create a new value prompt
![Value prompt](/img/20230106_0013/2_Value_prompt.png)

#### Select date and time 
![Date and time](/img/20230106_0013/3_Date_and_time.png)

#### Enter name of prompt
![Prompt name](/img/20230106_0013/4_prompt_name.png)

#### Save prompt
![Save prompt](/img/20230106_0013/5_Save_prmompt.png)

#### Register new metric
![New metric](/img/20230106_0013/6_register_new_metric.png)

{% highlight bash %}
ApplySimple("max(#0)"; ?[Comparison date]; Revenue)
{% endhighlight %}
?[Comparison date] is created prompt <br />
Revenue is rendom metric (needed for FROM statement) <br />

#### Convert date atribute into metric
![Date into metric](/img/20230106_0013/7_Date_To_Metric.png)
<br />
Do not forget to set it to MAX <br />

#### Calculate difference
![Calculate difference](/img/20230106_0013/8_Calculate_difference.png)
{% highlight bash %}
DateDiff(Date; [Comparison date]; "d")
{% endhighlight %}


#### Example
![Example](/img/20230106_0013/9example.png)
![Example2](/img/20230106_0013/9example2.png)


#### And now we can set clusters of data
![Example](/img/20230106_0013/10_Groups.png)
{% highlight bash %}
Case((DateDiff < 0); Revenue; 0)				Less then selected
Case((DateDiff = 0); Revenue; 0)				Selected date
Case(((DateDiff >=  1) And (DateDiff <= 30)); Revenue; 0)	Between 1-30
Case(((DateDiff >= 31) And (DateDiff <= 60)); Revenue; 0)	Between 31-60
Case(( DateDiff >= 61); Revenue; 0)				Greater then 60
{% endhighlight %}
