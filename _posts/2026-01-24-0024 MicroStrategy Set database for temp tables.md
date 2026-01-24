---
layout: post
title: MicroStrategy Set database for temp tables
tags:
- Microstrategy
- Temp tables
comments: true
---
Difficulty ★★☆☆☆
<br />

## Intro<br />
When working with MicroStrategy, temporary tables are created as part of normal report execution. In many environments, however, you may not want these objects to be created in the source database. Granting the MicroStrategy database user CREATE and DELETE permissions on a production schema is often undesirable, and temporary objects can unnecessarily clutter the source system.<br />

In this tutorial, I’ll show how to configure MicroStrategy to use a dedicated database (or schema) for temporary tables, keeping the source database clean while maintaining full reporting functionality. <br />

**Note:** After changing these settings, you need to restart the Intelligence Server for the change to take effect.<br />

First option: If you don’t care about the schema, you can configure the Database Instance to specify which database MicroStrategy should use for temporary tables. <br />
**Warning:** If you include a schema in this setting, MicroStrategy will append it incorrectly as 'db_name.schema..' (with double dots `..`). <br />

![BD_instance](/img/20260124_0024/BD_instance.png) <br />

The resulting SQL query looks like this: <br />
![DB_query](/img/20260124_0024/DB_query.png) <br />
As you can see, the SQL creates temporary tables in the 'MSTR_TEMP_TABLES' database.<br />

If you also want to use schemas, you need to set them in the Project Configuration under **SQL Data Warehouses → VLDB Properties**.<br />
![schema_project_conf](/img/20260124_0024/schema_project_conf.png) <br />

Under **Tables**, set the **Table Prefix** to your database and schema, separated by a dot (`.`), making sure it ends with a dot.<br />
![temp schema](/img/20260124_0024/temp_schema.png) <br />

The resulting SQL query, including schema details, looks like this:<br />
![schema_table](/img/20260124_0024/schema_table.png) <br />

With this configuration, MicroStrategy will create all temporary tables in your designated database and schema, keeping the source system clean and organized.

If you only want to test this on a specific report, change the VLDB Properties for that report to **Permanent Table**.<br />
![Report_setting](/img/20260124_0024/Report_setting.png)<br />
