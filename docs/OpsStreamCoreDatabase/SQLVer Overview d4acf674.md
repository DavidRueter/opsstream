We created SQLVer and released it as a stand-alone open source project (https://davidrueter.github.com/sqlver)

What is SQLVer?  It is a robust passive version management solution for Microsoft SQL Server.

What is "passive" version management?  SQLVer *automatically *logs every change that is made to the database:  adding / altering columns, stored procedures, views, functions, triggers and more.

The way it works, every time a DDL (data definition language) change is made (such as CREATE TABLE, ALTER PROCEDURE, etc.), a database-wide DDL trigger fires.  This allows SQLVer to parse the definition of the object, calculate a hash, save information about the object to a "manifest" (a list of all objects in the database), and save specific version information about the current version of the object.

In this way, SQLVer makes it easy to know for sure who changed what, when.  SQLVer also makes it easy to review and fall back to past versions of individual objects.

Since *all* changes are captured, SQLVer is good for both developer kinds of work and for DBA / performance tuning kinds of work.  For example, index changes result in a new hash being generated for the table, and incremental changes are logged for clarity and flexibility.

SQLVer's approach also makes it easy to compare objects in two different databases (such as Test and Production, or such as Master, Application1, Application2, etc.).  By reviewing the manifest data for each object in each database, it is easy to compare hashes to see if there is a difference between the two, and if so, which one is newer.  You can then go back through the version history in each database to find the most recent common hash (the last point when the versions matched), and retrieve the list of SQL commands that are needed to bring an older version up to match identically a later version.

SQLVer also provides simple ways to generate consistent copyright / attribution comments in each object, and to log special object-level and version-level comments in the manifest and/or change log tables.

SQLVer is part of the actual database itself.  It is in its own schema (sqlver).  This schema contains two tables (sqlver.tblSchemaManifest and sqlver.tblSchemaLog), the DDL trigger, and some supporting stored procedures and functions.  This means that you can expand upon the version capabilities of SQLVer quite easily.