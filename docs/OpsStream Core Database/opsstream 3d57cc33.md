The opsstream schema is the main schema for OpsStream.

Everything in the opsstream schema is managed by OpsStream.  OpsStream will dynamically create objects in this schema based on the configuration of the system.  User data and process definitions are also stored here.

While you are free to execute code in this schema and to read data (preferably from the views that OpsStream exposes), you should NOT directly add, modify, or delete any SQL objects (tables, stored procedures, functions, views, etc.)  OpsStream manages all of these, and may not work properly or may remove objects you create in this schema.

OpsStream does automatically manage indexes on tables and views in this schema.  You should not need to make direct changes to indexes, and should refrain from doing so.

If for performance tuning reasons you really need to make index changes, please contact us to let us know (so that we can consider incorporating your suggested change into the OpsStream system).  But if absolutely necessary, you may make index changes in the opsstream schema (at your own risk).

Naming conventions help keep things organized.  Tables that are named tblQXD_xxx contain user data.  (QXD stands for Quest eXternal Data).  These tables are created and maintained by OpsStream.

OpsStream also automatically creates and maintains CRUD (Create, Retrieve, Update, Delete) objects for these tblQXD_xxx tables.  For example, OpsStream creates and manages:

* vwQXD_xxx
* spupdQXD_xxx
* spinsQXD_xxx
* spgetQXD_xxx
* spdelQXD_xxx
* vwQXDLabel_xxx

OpsStream will automatically rebuild these objects (i.e. will drop and recreate them) as needed.  Do not make changes to these (or any) objects in the opsstream schema.