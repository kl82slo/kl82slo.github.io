---
layout: post
title: Microstrategy partition data
tags:
- Microstrategy
comments: true
---
Dificulty ★★★★☆


# In progress

### INTRO
If database queries take too long, there is a possibility to divide the data into several tables.<br /> 
In Microstrategy, however, we read them as from one table.

#### How to do it

Let's take the example of dividing the table by years.<br />
![Partition_Table](/img/20230618_0014/Partition table.png)

Now we need a table/view that will tell it to read these tables together. In our case, named PMT_YEARS.
{% highlight sql %}
CREATE TABLE [multi_data].[PMT_YEARS](
	[Leto] [smallint] NULL,
	[PBTNAME] [varchar](50) NULL
) ON [PRIMARY]
{% endhighlight %}


In the first column, enter the attribute by which the above tables should be filtered (Year). <br />
In the second column, the name of the table to be read. The second column is called <font color='red'>PBTNAME</font> <br />.
![Table](/img/20230618_0014/Table.png)


If you do not yet have a view in the database through which you will filter the Year, do this before the next part and register the attribute in MicroStrategy.
{% highlight sql %}
Create view [multi_data].[PMT_YEARS_ATRIBUTE_V] as
select distinct leto from [multi_data].[PMT_YEARS]
{% endhighlight %}
<br />
In the MicroStrategy 'Warehouse Catalog', add the table/view in which you entered the link between the attribute and the table.<br />
![Warehouse_Catalog](/img/20230618_0014/Werathose Catalog.png)

As you can see, it has a different shape than the other sources. <br />
If we click on 'view partition', we can see through which tables it is grouped.<br />
![which_tables](/img/20230618_0014/Witch tables.png)

Under 'Schema Objects' chose 'Partition Mappings'<br />
![Partition_Mappings](/img/20230618_0014/Partition Mappings.png)

We select the newly added entry. In 'Logical View' we add an attribute through which we will select the Year.<br />
![Logical_View](/img/20230618_0014/Logical View.png)

We add the PMT_YEARS to the Year attribute.<br />
![Year](/img/20230618_0014/Year.png)

When we register the other attributes, we must be aware that additional filters in the report will only be possible in the <font color='red'>star schema or snowflake</font>. <br />

#### Examples

If we filter year 2018. Query cheks witch tables to read.
{% highlight sql %}
select	distinct [a11].[PBTNAME]  [PBTNAME]
from	[multi_data].[PMT_YEARS]	[a11]
where	[a11].[Leto] in (2018)
{% endhighlight %}

Since we only need one it will read only that one.
{% highlight sql %}
select	[a11].[Drzava]  [Drzava],
	[a11].[Leto]  [Leto],
	[a11].[ST]  [WJXBFS1]
from	[multi_data].[PRIHODKI_2018]	[a11]
where	[a11].[Leto] in (2018)
{% endhighlight %}

In case we chose 3 years.
{% highlight sql %}
select	distinct [a11].[PBTNAME]  [PBTNAME]
from	[multi_data].[PMT_YEARS]	[a11]
where	[a11].[Leto] in (2018, 2019, 2020)
{% endhighlight %}

Query returns 3 joins.
{% highlight sql %}
select	[a11].[Drzava]  [Drzava],
	[a11].[Leto]  [Leto],
	[a11].[ST]  [WJXBFS1]
from	[multi_data].[PRIHODKI_2018]	[a11]
where	[a11].[Leto] in (2018, 2019, 2020)
union all
select	[a11].[Drzava]  [Drzava],
	[a11].[Leto]  [Leto],
	[a11].[ST]  [WJXBFS1]
from	[multi_data].[PRIHODKI_2019]	[a11]
where	[a11].[Leto] in (2018, 2019, 2020)
union all
select	[a11].[Drzava]  [Drzava],
	[a11].[Leto]  [Leto],
	[a11].[ST]  [WJXBFS1]
from	[multi_data].[PRIHODKI_2020]	[a11]
where	[a11].[Leto] in (2018, 2019, 2020)
{% endhighlight %}

If it is necessary to break down the data even more, this can be done with additional columns in the table/view where we added PBTNAME.<br />
![Multiple_colums](/img/20230618_0014/Multiple coulms.png)

The process is the same except that we add more attributes to 'Partition Mappings'<br />
![Multiple_colums_MSTR](/img/20230618_0014/Multiple coulms MSTR.png)



