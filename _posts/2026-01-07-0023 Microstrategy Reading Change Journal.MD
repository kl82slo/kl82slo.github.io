---
layout: post
title: Microstrategy Reading Change Journal
tags:
- Microstrategy
- Change Journal
comments: true
---
Difficulty ★★★☆☆
<br />
#IN PROGRESS

So a question came up: can we check how many changes a user has made in the current year?<br />

I decided to investigate further and came to conclusion to use Change Journal. <br />

and come to conclusion that<br />
DSSMDJRNOBJD - object was deleted <br />
DSSMDJRNOBJC - object was changed <br />
DSSMDJRNOBJS - change type<br />
DSSMDJRNINFO - object info (if it's not deleted)<br />


{% highlight sql %} 

with Transaction_type as
(
    SELECT *,
           case Transaction_type
                when 1 then 'Enable Change Journal'
                when 2 then 'Disable Change Journal'
                when 3 then 'Purge Change Journal'
                when 4 then 'Save Object'
                when 5 then 'Save Objects'
                when 6 then 'Save Link Item'
                when 7 then 'Delete Object'
                when 8 then 'Delete Objects'
                when 9 then 'Delete Project'
                when 10 then 'Copy Object'
                when 11 then 'Merge user'
                when 12 then 'Collect Garbage'
                when 13 then 'Write System Property'
                when 14 then 'Write Database Password'
                else 'unknown'
           end Trans_desc 
    FROM DSSMDJRNINFO 
)
, CHANGE_TYPE as
(
SELECT *,
        case CHANGE_TYPE
             when 1 then 'Create Object'
             when 2 then 'Change Object'
             when 3 then 'Delete Object'
             when 4 then 'Touch Folder'
             when 5 then 'Create Link Item'
             when 6 then 'Change Link Item'
             when 7 then 'Delete Link Item'
             when 8 then 'Enable Change Journal'
             when 9 then 'Disable Change Journal'
             when 10 then 'Purge Change Journal'
             else 'unknown'
        end change_Desc
  FROM DSSMDJRNOBJS
) , OBJECT_TYPE as
(
  SELECT distinct o.object_type,
         CASE o.object_type
            WHEN 1 THEN 'filter'
            WHEN 2 THEN 'template'
            WHEN 3 THEN 'report'
            WHEN 4 THEN 'metric'
            WHEN 6 THEN 'autostyle'
            WHEN 7 THEN 'fact'
            WHEN 8 THEN 'folder'
            WHEN 10 THEN 'prompt'
            WHEN 11 THEN 'function'
            WHEN 12 THEN 'attribute'
            WHEN 13 THEN 'fact'
            WHEN 14 THEN 'hierarchy'
            WHEN 15 THEN 'table'
            WHEN 17 THEN 'fact group'
            WHEN 18 THEN 'shortcut'
			WHEN 19 THEN 'prompt anser'
            WHEN 21 THEN 'attribute id'
            WHEN 22 THEN 'schema'
            WHEN 24 THEN 'warehouse catalog'
            WHEN 25 THEN 'warehouse catalog definition'
            WHEN 26 THEN 'table column'
            WHEN 28 THEN 'property sets'
            WHEN 29 THEN 'DB Instance'
            WHEN 30 THEN 'DB Login'
            WHEN 31 THEN 'DB Description'
			WHEN 33 THEN 'Server definition'
            WHEN 34 THEN 'users/groups'
            WHEN 39 THEN 'search'
			WHEN 40 THEN 'search folder'
            WHEN 42 THEN 'package'
			WHEN 43 THEN 'Transformations'
            WHEN 47 THEN 'consolidations'
            WHEN 52 THEN 'link'
            WHEN 53 THEN 'table'
			WHEN 55 THEN 'Document'
            WHEN 56 THEN 'drill map'
            WHEN 58 THEN 'security filter'
            ELSE 'OTHERS'				/*https://community.microstrategy.com/s/article/KB16048-List-of-all-object-types-and-object-descriptions-in?language=en_US*/
         END
            AS tipo
    FROM dssmdobjinfo o
)
select	  d.OBJECT_NAME																						as Project
		, coalesce(created.object_name,Deleted.OBJECT_NAME,NameOfChangedAndDeletedObject.OBJECT_NAME)		as Objekt
		, coalesce(Deleted.OBJECT_TYPE,created.OBJECT_TYPE,NameOfChangedAndDeletedObject.OBJECT_TYPE)		as ObjectTypeId
		, coalesce(Deleted_object_type.tipo,Created_object_type.tipo,ChangedAndDeleted_object_type.tipo)	as ObjectType
		--, a.Transaction_type
		, a.trans_Desc									as Trans_Desc
		, a.TRANSACTION_TIMESTAMP						as Transaction_Timestamp
  		--, a.USER_ID
		, u.OBJECT_NAME									as User_Name
		, a.MACHINE										as AccesFrom
		, Changed.COMMENT_VAL							as Change_notification
		, iif( deleted.OBJECT_NAME is not null,1,0)		as Count_Deletes
		, iif( Changed.TRANSACTION_ID is not null,1,0)	as Count_Changes
		, iif( c.CHANGE_TYPE = 1 ,1,0)					as Count_Created
		, iif( a.Transaction_type = 5 ,1,0)				as Count_Saved
		--, Deleted.OBJECT_NAME							as NameOfDeletedObject
		--, created.object_name							as NameOfChangedObject
		--, NameOfChangedAndDeletedObject.object_name		as NameOfChangedAndDeletedObject
		--, Deleted.OBJECT_ID								as deleted_id
		--, Changed.OBJECT_ID								as changed_id
		--, created.OBJECT_ID								as created_id
from Transaction_type							AS a			--transactions
left join DSSMDJRNOBJD							AS Deleted		--deleted (base)
on a.TRANSACTION_ID = Deleted.TRANSACTION_ID
left join CHANGE_TYPE							AS c			--created (base)
on a.TRANSACTION_ID = c.TRANSACTION_ID 
left join DSSMDOBJINFO							AS d			--project name
on c.project_id = d.OBJECT_ID
LEFT JOIN DSSMDOBJINFO							AS u			--user
ON a.USER_ID = u.OBJECT_ID
    AND u.OBJECT_TYPE IN (8, 34)
LEFT JOIN OBJECT_TYPE							AS Deleted_object_type	--object type for deleted
on  deleted.OBJECT_TYPE = Deleted_object_type.object_type
LEFT JOIN [dbo].[DSSMDJRNOBJC]					AS Changed		--changed (base)
on a.TRANSACTION_ID = Changed.TRANSACTION_ID
and a.TRANSACTION_PROJECT_ID = Changed.PROJECT_ID
LEFT JOIN DSSMDOBJINFO							AS Created		--details for creted
on c.OBJECT_ID = created.OBJECT_ID
and c.PROJECT_ID = created.PROJECT_ID
LEFT JOIN OBJECT_TYPE							AS Created_object_type	--object type for created
on  created.OBJECT_TYPE = Created_object_type.object_type
LEFT JOIN DSSMDJRNOBJD							AS NameOfChangedAndDeletedObject--changed and then deleted - Get Name from deleted since ID of object exist
on Changed.OBJECT_ID = NameOfChangedAndDeletedObject.OBJECT_ID
LEFT JOIN OBJECT_TYPE							AS ChangedAndDeleted_object_type	--	 object type for changed that were deleted
on  NameOfChangedAndDeletedObject.OBJECT_TYPE = ChangedAndDeleted_object_type.object_type
where 
CHANGE_TYPE <> 4		/*if you save a report we dont need folder to be reported as something changing*/
--and d.OBJECT_NAME = 'Razvojno okolje Klemen'		--filter project
--and year(a.TRANSACTION_TIMESTAMP) = 2025			--filter year
  order by d.OBJECT_NAME, a.TRANSACTION_TIMESTAMP desc

{% endhighlight %}
<br />

## TESTING<br />
If you add a new report and don't add notification it will show it as<br />
![01](/img/20260107_0023/01.png)

if we chage the name of it shows as<br />
![02](/img/20260107_0023/02.png)

if you just open it and save nothing changes<br />
but if you let's say add another atribute you get<br />
![03](/img/20260107_0023/03.png)

and now when you delete it 
![04](/img/20260107_0023/04.png)

we can read that something was creted and saved we know what was changed and what was deleted but we can't say it was the same object since we can olny see transaction_id and not object_id<br />

if we do the same just use comments this time<br />
![05](/img/20260107_0023/05.png)

we get<br />
![06](/img/20260107_0023/06.png)
even after it was deleted


Notes:
The idia I got from 
[microstrategy-change-journal](https://bestdatasolutions.es/en/blog/microstrategy-change-journal/)




