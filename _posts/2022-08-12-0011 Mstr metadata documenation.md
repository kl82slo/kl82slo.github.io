---
layout: post
title: MicroStrategy - Tables and column used
tags:
- MicroStrategy
- Documentation
- Tables
- Columns
- Metadata
comments: true
---
Dificulty ★☆☆☆☆


## Intro
Following the idea from [mstr-metadata-tables](http://khaidoan.wikidot.com/mstr-metadata-tables) we can use this logic to create documentation how each attribute/fact is connected to which table/column.

## Understanding difrent types

Since we will be looking into DSSMDOBJINFO table it is good to know what certen object type mean.

{% highlight sql %}  
  SELECT o.project_id,
         CASE o.object_type
            WHEN 1 THEN 'filter (1)  '
            WHEN 2 THEN 'template (2)'
            WHEN 3 THEN 'report (3)'
            WHEN 4 THEN 'metric (4)'
            WHEN 6 THEN 'autostyle (6)'
            WHEN 8 THEN 'folder (8)'
            WHEN 10 THEN 'prompt (10)'
            WHEN 11 THEN 'function (11)'
            WHEN 12 THEN 'attribute (12)'
            WHEN 13 THEN 'fact (13)'
            WHEN 14 THEN 'hierarchy (14)'
            WHEN 15 THEN 'table (15)'
            WHEN 21 THEN 'attribute id (21)'
            WHEN 22 THEN 'schema (22)'
            WHEN 24 THEN 'warehouse catalog (24)'
            WHEN 25 THEN 'warehouse catalog definition (25)'
            WHEN 26 THEN 'table column (26)'
            WHEN 28 THEN 'property sets (28)'
            WHEN 34 THEN 'users/groups (34)'
            WHEN 39 THEN 'search (39)'
            WHEN 42 THEN 'package (42)'
            WHEN 47 THEN 'consolidations (47)'
            WHEN 52 THEN 'link (52)'
            WHEN 53 THEN 'table (53)'
            WHEN 56 THEN 'drill map (56)'
            WHEN 58 THEN 'security filter (58)'
            ELSE 'OTHERS'
         END
            AS tipo,
         o.object_name,
         o.object_id
    FROM dssmdobjinfo o
ORDER BY tipo
	
{% endhighlight %}


## Atributes

For attributes the sql is pretty straight forward since we only need to find connection between attributes, columns and tables.

{% highlight sql %}  
WITH parameters AS (
SELECT '9229CB8F-4DFA-4925-9CD1-0CFF9E9E11A5' AS PROJECT_ID
)
, atributes AS (
	SELECT
			PROJECT_ID
		,	OBJECT_ID      AS ATT_GUID
		,	OBJECT_TYPE    AS ATT_TYPE_ID
		,	OBJECT_NAME    AS ATT_NAME
		,	CREATE_TIME    AS ATT_CREATE_TIME
		,	MOD_TIME       AS ATT_MOD_TIME
	FROM DSSMDOBJINFO             
	WHERE
	    OBJECT_TYPE IN (12) /*atributes*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND OBJECT_STATE = 0
)
, tables AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID    AS TABLE_GUID
		,	i.OBJECT_NAME  AS TABLE_NAME
		,	i.CREATE_TIME  AS TABLE_CREATE_TIME
		,	i.MOD_TIME     AS TABLE_MOD_TIME
	FROM DSSMDOBJINFO	i
	WHERE
	    i.OBJECT_TYPE IN (53) /*tables*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND OBJECT_STATE = 0
)
, columns AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID    AS COLUMN_GUID
		,	i.OBJECT_NAME  AS COLUMN_NAME
		,	i.CREATE_TIME  AS COLUMN_CREATE_TIME
		,	i.MOD_TIME     AS COLUMN_MOD_TIME
	FROM DSSMDOBJINFO	i
	WHERE
	    i.OBJECT_TYPE IN (26) /*column*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND OBJECT_STATE = 0
)
, rltd_TableColumn AS (
	SELECT
			PROJECT_ID     AS PROJECT_ID
		,	OBJECT_ID      AS TABLE_GUID
		,	DEPN_OBJID     AS COLUMN_GUID

	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (53)   /*tables*/	
	AND DEPNOBJ_TYPE IN (26)   /*column*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_AttributeColumn AS (
	SELECT
			PROJECT_ID     AS PROJECT_ID
		,	OBJECT_ID      AS ATT_GUID
		,	DEPN_OBJID     AS COLUMN_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE IN (12)   /*atributes*/ 
	AND DEPNOBJ_TYPE IN (26)  /*column*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters) 
)
    SELECT DISTINCT
            att.PROJECT_ID
        ,   att.ATT_GUID
    --  ,   tab.TABLE_GUID
    --  ,   col.COLUMN_GUID
        ,   att.ATT_NAME
        ,   tab.TABLE_NAME
    --  ,   tab.TABLE_CREATE_TIME
    --  ,   tab.TABLE_MOD_TIME
        ,   col.COLUMN_NAME
    --  ,   col.COLUMN_CREATE_TIME
    --  ,   col.COLUMN_MOD_TIME
    FROM atributes                              att
    LEFT JOIN rltd_AttributeColumn              rAC
        ON	att.PROJECT_ID  = rAC.PROJECT_ID
        AND     att.ATT_GUID    = rAC.ATT_GUID
    LEFT JOIN columns                           col
        ON	rAC.PROJECT_ID  = col.PROJECT_ID
        AND	rAC.COLUMN_GUID = col.COLUMN_GUID
    LEFT JOIN rltd_TableColumn                  rTC
        ON	col.PROJECT_ID  = rTC.PROJECT_ID
        AND	col.COLUMN_GUID = rTC.COLUMN_GUID
    LEFT JOIN tables                            tab
        ON	rTC.PROJECT_ID  = tab.PROJECT_ID
        AND     rTC.TABLE_GUID  = tab.TABLE_GUID
	WHERE TABLE_NAME IS NOT NULL AND len(tab.TABLE_NAME) > 1
	ORDER by ATT_NAME
	
{% endhighlight %}

![Atribute](/img/20220812_0011/Atributes1.png)

## Metrics

For metrics things get complicated since we need connection between metric, fact, column and table.

{% highlight sql %}  
------ ReportStatistics ------
WITH parameters AS (
SELECT '9229CB8F-4DFA-4925-9CD1-0CFF9E9E11A5' AS PROJECT_ID
)
, metric AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID       AS METRIC_GUID
		--,	i.OBJECT_TYPE     AS ATT_TYPE_ID
		,	i.OBJECT_NAME     AS METRIC_NAME
		,	i.CREATE_TIME     AS METRIC_CREATE_TIME
		,	i.MOD_TIME        AS METRIC_MOD_TIME
	FROM DSSMDOBJINFO i
 
	WHERE
		i.OBJECT_TYPE IN (4)	/*metric*/
	AND i.PROJECT_ID  IN (SELECT PROJECT_ID FROM parameters) 
	AND i.OBJECT_STATE = 0
)
, fact AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID       AS FACT_GUID
		--,	i.OBJECT_TYPE	  AS ATT_TYPE_ID
		,	i.OBJECT_NAME     AS FACT_NAME
		,	i.CREATE_TIME     AS FACT_CREATE_TIME
		,	i.MOD_TIME        AS FACT_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (13)	/*fact*/
	AND i.PROJECT_ID  IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, tables AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID       AS TABLE_GUID
		--,	i.OBJECT_TYPE     AS TABLE_TYPE_ID
		,	i.OBJECT_NAME     AS TABLE_NAME
		,	i.CREATE_TIME     AS TABLE_CREATE_TIME
		,	i.MOD_TIME        AS TABLE_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (53) /*table*/
	AND i.PROJECT_ID  IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, columns AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID 	AS COLUMN_GUID
		--,	i.OBJECT_TYPE	AS COLUMN_TYPE_ID
		,	i.OBJECT_NAME	AS COLUMN_NAME
		,	i.CREATE_TIME	AS COLUMN_CREATE_TIME
		,	i.MOD_TIME      AS COLUMN_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (26)	/*column*/
	AND i.PROJECT_ID  IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, rltd_TableColumn AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS TABLE_GUID
		,	DEPN_OBJID  AS COLUMN_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (53)	/*table*/
	AND DEPNOBJ_TYPE IN (26)	/*column*/
	AND PROJECT_ID   IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_Metric_fact AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS METRIC_GUID
		,	DEPN_OBJID  AS FACT_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (4)		/*metric*/
	AND DEPNOBJ_TYPE IN (13)	/*fact*/
	AND PROJECT_ID	 IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_Fact_To_Table AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS FACT_GUID
		,	DEPN_OBJID  AS COLUMN_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (13)	/*fact*/
	AND DEPNOBJ_TYPE IN (26)	/*column*/
	AND PROJECT_ID	 IN (SELECT PROJECT_ID FROM parameters)
)

    SELECT distinct
    	  met.PROJECT_ID
	, rTC.TABLE_GUID
    	, met.METRIC_GUID
	, met.FACT_GUID
	, rTC.COLUMN_GUID
	, m.METRIC_NAME
    	, rAC.FACT_NAME
    	, col.COLUMN_NAME	AS DB_Column
	, tab.TABLE_NAME	AS DB_Table
    FROM rltd_Metric_fact                          met
    LEFT JOIN fact                                 rAC
         ON     met.PROJECT_ID	= rAC.PROJECT_ID
         AND    met.FACT_GUID	= rAC.FACT_GUID
    LEFT JOIN rltd_Fact_To_Table                   FTF
         ON     met.PROJECT_ID	= FTF.PROJECT_ID
         AND    met.FACT_GUID	= ftf.FACT_GUID
    LEFT JOIN columns                              col
         ON	rAC.PROJECT_ID  = col.PROJECT_ID
         AND	FTF.COLUMN_GUID	= col.COLUMN_GUID
    LEFT JOIN rltd_TableColumn                     rTC
         ON	col.PROJECT_ID  = rTC.PROJECT_ID
         AND	col.COLUMN_GUID = rTC.COLUMN_GUID
    LEFT JOIN tables                               tab
         ON     rTC.PROJECT_ID  = tab.PROJECT_ID
         AND    rTC.TABLE_GUID  = tab.TABLE_GUID
    LEFT JOIN metric                               m
         ON	met.PROJECT_ID	= m.PROJECT_ID
         AND    met.METRIC_GUID = m.METRIC_GUID
       WHERE rTC.column_guid is not null
         AND tab.TABLE_GUID  is not null
{% endhighlight %}

So if we have<br /> 
![Metrics](/img/20220812_0011/Metrics.png)

This will give as only metrics that are directly connected to fact.<br /> 
![Metrics_Normal](/img/20220812_0011/Metric_Normal.png)


## Metrics containing metrics

Since we use metrics inside metrics we need to include them to.
![Metric calculation](/img/20220812_0011/Metric_sum.png)


{% highlight sql %}  
------ ReportStatistics ------
WITH parameters AS (
SELECT '9229CB8F-4DFA-4925-9CD1-0CFF9E9E11A5' AS PROJECT_ID
)
, metric AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID       AS METRIC_GUID
		--,	i.OBJECT_TYPE     AS ATT_TYPE_ID
		,	i.OBJECT_NAME     AS METRIC_NAME
		,	i.CREATE_TIME     AS METRIC_CREATE_TIME
		,	i.MOD_TIME        AS METRIC_MOD_TIME
	FROM DSSMDOBJINFO i
 
	WHERE
	    i.OBJECT_TYPE IN (4) /*metric*/
	AND i.PROJECT_ID IN (SELECT PROJECT_ID FROM parameters) 
	AND i.OBJECT_STATE = 0
)
, fact AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID       AS FACT_GUID
		--,	i.OBJECT_TYPE     AS ATT_TYPE_ID
		,	i.OBJECT_NAME     AS FACT_NAME
		,	i.CREATE_TIME     AS FACT_CREATE_TIME
		,	i.MOD_TIME        AS FACT_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (13)   /*fact*/
	AND i.PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, tables AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID 	AS TABLE_GUID
		--,	i.OBJECT_TYPE	AS TABLE_TYPE_ID
		,	i.OBJECT_NAME   AS TABLE_NAME
		,	i.CREATE_TIME   AS TABLE_CREATE_TIME
		,	i.MOD_TIME 	AS TABLE_MOD_TIME
	FROM DSSMDOBJINFO	i
	WHERE
	    i.OBJECT_TYPE IN (53)   /*table*/
	AND i.PROJECT_ID  IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, columns AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID 	AS COLUMN_GUID
		--,	i.OBJECT_TYPE	AS COLUMN_TYPE_ID
		,	i.OBJECT_NAME	AS COLUMN_NAME
		,	i.CREATE_TIME	AS COLUMN_CREATE_TIME
		,	i.MOD_TIME      AS COLUMN_MOD_TIME
	FROM DSSMDOBJINFO	i
	WHERE
	    i.OBJECT_TYPE IN (26)	/*column*/
	AND i.PROJECT_ID  IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, rltd_TableColumn AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS TABLE_GUID
		,	DEPN_OBJID  AS COLUMN_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (53)	/*table*/
	AND DEPNOBJ_TYPE IN (26)	/*column*/
	AND PROJECT_ID   IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_Metric_fact AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS METRIC_GUID
		,	DEPN_OBJID  AS FACT_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (4)		/*metric*/
	AND DEPNOBJ_TYPE IN (13)	/*fact*/
	AND PROJECT_ID	 IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_Fact_To_Table AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS FACT_GUID
		,	DEPN_OBJID  AS COLUMN_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (13)	/*fact*/
	AND DEPNOBJ_TYPE IN (26)	/*column*/
	AND PROJECT_ID	 IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_Metric_to_Metric AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS METRIC_GUID
		,	DEPN_OBJID  AS METRIC_GUID_DOWN

	FROM DSSMDOBJDEPN a
	WHERE
	    OBJECT_TYPE IN (4,13)
	AND DEPNOBJ_TYPE IN (4,13)
	AND PROJECT_ID	 IN (SELECT PROJECT_ID FROM parameters)
)
, metric_to_metric_conection as (
 	SELECT
			M.PROJECT_ID
		,	M.METRIC_GUID
		,	m.METRIC_GUID_DOWN  as m2
		,	M2.METRIC_GUID_DOWN as m3
		,	M3.METRIC_GUID_DOWN as m4
	FROM  rltd_Metric_to_Metric M

	left join rltd_Metric_to_Metric M2
	ON  M.METRIC_GUID_DOWN = M2.METRIC_GUID
	AND M.PROJECT_ID = M2.PROJECT_ID

	left join rltd_Metric_to_Metric M3
	ON  M2.METRIC_GUID_DOWN = M3.METRIC_GUID
	AND M2.PROJECT_ID = M3.PROJECT_ID
)
, metrtics_and_final_facts as (
 select PROJECT_ID, METRIC_GUID, m2 as FACT_GUID from  metric_to_metric_conection
where   (
   m2 in (select FACT_GUID from fact)) 
union
 select PROJECT_ID, METRIC_GUID, m3 as FACT_GUID from  metric_to_metric_conection
where  (
   m3 in (select FACT_GUID from fact))
union
 select PROJECT_ID, METRIC_GUID, m4 as FACT_GUID from  metric_to_metric_conection
where  (
   m4 in (select FACT_GUID from fact))
)
    SELECT distinct
    	  met.PROJECT_ID
	, rTC.TABLE_GUID
    	, met.METRIC_GUID
	, met.FACT_GUID
	, rTC.COLUMN_GUID
	, m.METRIC_NAME
    	, rAC.FACT_NAME
    	, col.COLUMN_NAME	AS DB_Column
	, tab.TABLE_NAME	AS DB_Table
    FROM metrtics_and_final_facts                  met
    LEFT JOIN fact                                 rAC
        ON	met.PROJECT_ID	= rAC.PROJECT_ID
        AND     met.FACT_GUID	= rAC.FACT_GUID
   LEFT JOIN rltd_Fact_To_Table                    FTF
        ON	met.PROJECT_ID	= FTF.PROJECT_ID
        AND     met.FACT_GUID	= ftf.FACT_GUID
   LEFT JOIN columns                               col
        ON	rAC.PROJECT_ID  = col.PROJECT_ID
        AND	FTF.COLUMN_GUID	= col.COLUMN_GUID
    LEFT JOIN rltd_TableColumn                     rTC
        ON	col.PROJECT_ID  = rTC.PROJECT_ID
        AND	col.COLUMN_GUID = rTC.COLUMN_GUID
    LEFT JOIN tables                               tab
        ON	rTC.PROJECT_ID  = tab.PROJECT_ID
        AND     rTC.TABLE_GUID  = tab.TABLE_GUID
    LEFT JOIN metric                               m
        ON	met.PROJECT_ID	= m.PROJECT_ID
        AND     met.METRIC_GUID = m.METRIC_GUID
    WHERE    rTC.column_guid is not null
        AND  tab.TABLE_GUID  is not null
{% endhighlight %}

In this we see case metric 'Countries ACT' is combined from 2 different facts
![Metric calculation Detailes](/img/20220812_0011/Metric_Detail.png)


## Atributes used in reports 

If you want to know which report uses which attribute you can do it like this


{% highlight sql %}  
 
WITH parameters AS (
SELECT '9229CB8F-4DFA-4925-9CD1-0CFF9E9E11A5' AS PROJECT_ID
)
, atributes AS (
	SELECT
			PROJECT_ID
		,	OBJECT_ID      AS ATT_GUID
		,	OBJECT_TYPE    AS ATT_TYPE_ID
		,	OBJECT_NAME    AS ATT_NAME
		,	CREATE_TIME    AS ATT_CREATE_TIME
		,	MOD_TIME       AS ATT_MOD_TIME
	FROM DSSMDOBJINFO             
	WHERE
	    OBJECT_TYPE IN (12) /*atributes*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND OBJECT_STATE = 0
)
, tables AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID    AS TABLE_GUID
		,	i.OBJECT_NAME  AS TABLE_NAME
		,	i.CREATE_TIME  AS TABLE_CREATE_TIME
		,	i.MOD_TIME     AS TABLE_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (53) /*tables*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND OBJECT_STATE = 0
)
, columns AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID    AS COLUMN_GUID
		,	i.OBJECT_NAME  AS COLUMN_NAME
		,	i.CREATE_TIME  AS COLUMN_CREATE_TIME
		,	i.MOD_TIME     AS COLUMN_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (26) /*column*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND OBJECT_STATE = 0
)
, rltd_TableColumn AS (
	SELECT
			PROJECT_ID     AS PROJECT_ID
		,	OBJECT_ID      AS TABLE_GUID
		,	DEPN_OBJID     AS COLUMN_GUID

	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (53)   /*tables*/	
	AND DEPNOBJ_TYPE IN (26)   /*column*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_AttributeColumn AS (
	SELECT
	   	PROJECT_ID     AS PROJECT_ID
	   ,	OBJECT_ID      AS ATT_GUID
	   ,	DEPN_OBJID     AS COLUMN_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE IN (12)   /*atributes*/ 
	AND DEPNOBJ_TYPE IN (26)  /*column*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters) 
)
, rltd_ReportAtribute AS (
      SELECT
	   	PROJECT_ID     AS PROJECT_ID
	   ,	OBJECT_ID      AS REP_GUID
	   ,	DEPN_OBJID     AS ATT_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (3)   /*report*/	
	AND DEPNOBJ_TYPE IN (12)  /*atribute*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters) 
)
, reports AS (
	SELECT
		PROJECT_ID
	   ,	OBJECT_ID      AS REP_GUID
	   ,	OBJECT_TYPE    AS REP_TYPE_ID
	   ,	OBJECT_NAME    AS REP_NAME
	   ,	CREATE_TIME    AS REP_CREATE_TIME
	   ,	MOD_TIME       AS REP_MOD_TIME
	FROM DSSMDOBJINFO             
	WHERE
	    OBJECT_TYPE IN (3) /*reports*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND OBJECT_STATE = 0

)
    SELECT DISTINCT
            att.PROJECT_ID
        ,   rep.REP_GUID
        ,   att.ATT_GUID
    --  ,   tab.TABLE_GUID
    --  ,   col.COLUMN_GUID
        ,   rep.REP_NAME
        ,   att.ATT_NAME
        ,   tab.TABLE_NAME
    --  ,   tab.TABLE_CREATE_TIME
    --  ,   tab.TABLE_MOD_TIME
        ,   col.COLUMN_NAME
    --  ,   col.COLUMN_CREATE_TIME
    --  ,   col.COLUMN_MOD_TIME
	from rltd_ReportAtribute                  rRA
	LEFT JOIN atributes                       att
        ON	rRA.PROJECT_ID  = att.PROJECT_ID
        AND    	rRA.ATT_GUID    = att.ATT_GUID
    LEFT JOIN rltd_AttributeColumn                rAC
        ON	att.PROJECT_ID  = rAC.PROJECT_ID
        AND     att.ATT_GUID    = rAC.ATT_GUID
    LEFT JOIN columns                             col
        ON	rAC.PROJECT_ID  = col.PROJECT_ID
        AND	rAC.COLUMN_GUID = col.COLUMN_GUID
    LEFT JOIN rltd_TableColumn                    rTC
        ON	col.PROJECT_ID  = rTC.PROJECT_ID
        AND	col.COLUMN_GUID = rTC.COLUMN_GUID
    LEFT JOIN tables                              tab
        ON	rTC.PROJECT_ID  = tab.PROJECT_ID
        AND     rTC.TABLE_GUID  = tab.TABLE_GUID
    LEFT JOIN reports                             rep
        ON	rRA.PROJECT_ID  = rep.PROJECT_ID
        AND     rRA.REP_GUID    = rep.REP_GUID
	WHERE TABLE_NAME IS NOT NULL AND len(tab.TABLE_NAME) > 1
	ORDER by REP_NAME, ATT_NAME
{% endhighlight %}




## Metrics used in reports 

And same for metrics

{% highlight sql %}  
------ ReportStatistics ------
WITH parameters AS (
SELECT '9229CB8F-4DFA-4925-9CD1-0CFF9E9E11A5' AS PROJECT_ID
)
, metric AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID       AS METRIC_GUID
		--,	i.OBJECT_TYPE     AS ATT_TYPE_ID
		,	i.OBJECT_NAME     AS METRIC_NAME
		,	i.CREATE_TIME     AS METRIC_CREATE_TIME
		,	i.MOD_TIME        AS METRIC_MOD_TIME
	FROM DSSMDOBJINFO i
 
	WHERE
	    i.OBJECT_TYPE IN (4) /*metric*/
	AND i.PROJECT_ID IN (SELECT PROJECT_ID FROM parameters) 
	AND i.OBJECT_STATE = 0
)
, fact AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID       AS FACT_GUID
		--,	i.OBJECT_TYPE     AS ATT_TYPE_ID
		,	i.OBJECT_NAME     AS FACT_NAME
		,	i.CREATE_TIME     AS FACT_CREATE_TIME
		,	i.MOD_TIME        AS FACT_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (13)   /*fact*/
	AND i.PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, tables AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID 	AS TABLE_GUID
		--,	i.OBJECT_TYPE	AS TABLE_TYPE_ID
		,	i.OBJECT_NAME   AS TABLE_NAME
		,	i.CREATE_TIME   AS TABLE_CREATE_TIME
		,	i.MOD_TIME 	AS TABLE_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (53)   /*table*/
	AND i.PROJECT_ID  IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, columns AS (
	SELECT
			i.PROJECT_ID
		,	i.OBJECT_ID 	AS COLUMN_GUID
		--,	i.OBJECT_TYPE	AS COLUMN_TYPE_ID
		,	i.OBJECT_NAME	AS COLUMN_NAME
		,	i.CREATE_TIME	AS COLUMN_CREATE_TIME
		,	i.MOD_TIME      AS COLUMN_MOD_TIME
	FROM DSSMDOBJINFO i
	WHERE
	    i.OBJECT_TYPE IN (26)	/*column*/
	AND i.PROJECT_ID  IN (SELECT PROJECT_ID FROM parameters)
	AND i.OBJECT_STATE = 0
)
, rltd_TableColumn AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS TABLE_GUID
		,	DEPN_OBJID  AS COLUMN_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (53)	/*table*/
	AND DEPNOBJ_TYPE IN (26)	/*column*/
	AND PROJECT_ID   IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_Metric_fact AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS METRIC_GUID
		,	DEPN_OBJID  AS FACT_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (4)		/*metric*/
	AND DEPNOBJ_TYPE IN (13)	/*fact*/
	AND PROJECT_ID	 IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_Fact_To_Table AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS FACT_GUID
		,	DEPN_OBJID  AS COLUMN_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (13)	/*fact*/
	AND DEPNOBJ_TYPE IN (26)	/*column*/
	AND PROJECT_ID	 IN (SELECT PROJECT_ID FROM parameters)
)
, rltd_Metric_to_Metric AS (
	SELECT
			PROJECT_ID  AS PROJECT_ID
		,	OBJECT_ID   AS METRIC_GUID
		,	DEPN_OBJID  AS METRIC_GUID_DOWN

	FROM DSSMDOBJDEPN a
	WHERE
	    OBJECT_TYPE IN (4,13)
	AND DEPNOBJ_TYPE IN (4,13)
	AND PROJECT_ID	 IN (SELECT PROJECT_ID FROM parameters)
)
, metric_to_metric_conection as (
 	SELECT
			M.PROJECT_ID
		,	M.METRIC_GUID
		,	m.METRIC_GUID_DOWN  as m2
		,	M2.METRIC_GUID_DOWN as m3
		,	M3.METRIC_GUID_DOWN as m4
	FROM  rltd_Metric_to_Metric M

	left join rltd_Metric_to_Metric M2
	ON  M.METRIC_GUID_DOWN = M2.METRIC_GUID
	AND M.PROJECT_ID = M2.PROJECT_ID

	left join rltd_Metric_to_Metric M3
	ON  M2.METRIC_GUID_DOWN = M3.METRIC_GUID
	AND M2.PROJECT_ID = M3.PROJECT_ID
)
, metrtics_and_final_facts as (
 select PROJECT_ID, METRIC_GUID, m2 as FACT_GUID from  metric_to_metric_conection
where   (
   m2 in (select FACT_GUID from fact)) 
union
 select PROJECT_ID, METRIC_GUID, m3 as FACT_GUID from  metric_to_metric_conection
where  (
   m3 in (select FACT_GUID from fact))
union
 select PROJECT_ID, METRIC_GUID, m4 as FACT_GUID from  metric_to_metric_conection
where  (
   m4 in (select FACT_GUID from fact))
)
, rltd_ReportMetric AS (
	SELECT
			PROJECT_ID     AS PROJECT_ID
		,	OBJECT_ID      AS REP_GUID
		,	DEPN_OBJID     AS METRIC_GUID
	FROM DSSMDOBJDEPN
	WHERE
	    OBJECT_TYPE  IN (3)   /*report*/	
	AND DEPNOBJ_TYPE IN (4)   /*metric*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters) 
)
, reports AS (
	SELECT
			PROJECT_ID
		,	OBJECT_ID      AS REP_GUID
		,	OBJECT_TYPE    AS REP_TYPE_ID
		,	OBJECT_NAME    AS REP_NAME
		,	CREATE_TIME    AS REP_CREATE_TIME
		,	MOD_TIME       AS REP_MOD_TIME
	FROM DSSMDOBJINFO             
	WHERE
	    OBJECT_TYPE IN (3)  /*reports*/
	AND PROJECT_ID IN (SELECT PROJECT_ID FROM parameters)
	AND OBJECT_STATE = 0
)
    SELECT distinct
    	  met.PROJECT_ID
    	, rRM.REP_GUID
    	, rTC.TABLE_GUID
    	, met.METRIC_GUID
    	, met.FACT_GUID
    	, rTC.COLUMN_GUID
    	, rep.REP_NAME
    	, m.METRIC_NAME
    	, rAC.FACT_NAME
    	, col.COLUMN_NAME	AS DB_Column
    	, tab.TABLE_NAME	AS DB_Table
    FROM rltd_ReportMetric                         rRM
	LEFT JOIN metrtics_and_final_facts         met
        ON      rRM.PROJECT_ID	= met.PROJECT_ID
        AND     rRM.METRIC_GUID	= met.METRIC_GUID
    LEFT JOIN fact                                 rAC
        ON	met.PROJECT_ID	= rAC.PROJECT_ID
        AND     met.FACT_GUID	= rAC.FACT_GUID
    LEFT JOIN rltd_Fact_To_Table                   FTF
        ON	met.PROJECT_ID	= FTF.PROJECT_ID
        AND     met.FACT_GUID	= ftf.FACT_GUID
    LEFT JOIN columns                              col
        ON	rAC.PROJECT_ID  = col.PROJECT_ID
        AND	FTF.COLUMN_GUID	= col.COLUMN_GUID
    LEFT JOIN rltd_TableColumn                     rTC
        ON	col.PROJECT_ID  = rTC.PROJECT_ID
        AND	col.COLUMN_GUID = rTC.COLUMN_GUID
    LEFT JOIN tables                               tab
        ON	rTC.PROJECT_ID  = tab.PROJECT_ID
        AND     rTC.TABLE_GUID  = tab.TABLE_GUID
    LEFT JOIN metric                               m
        ON	met.PROJECT_ID	= m.PROJECT_ID
        AND     met.METRIC_GUID = m.METRIC_GUID
    LEFT JOIN reports                              rep
        ON	rRM.PROJECT_ID  = rep.PROJECT_ID
        AND     rRM.REP_GUID    = rep.REP_GUID
     WHERE rTC.column_guid is not null
        AND  tab.TABLE_GUID  is not null
{% endhighlight %}
