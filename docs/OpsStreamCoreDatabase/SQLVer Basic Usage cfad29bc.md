## Making Changes

SQLVer is installed and active by default.  There is nothing you need to do, except make your changes to SQL in the normal way (using whatever tools and workflows you desire).

Supposing you are using an interactive tool like SSMS (SQLServer Management Studio), every time you execute a DDL command (such as ALTER TABLE), you will see a friendly message returned:

`SQLVer successfully logged CREATE_PROCEDURE osDev.dbo.spTest (Hash: 0x2d072b4a4999d767972b3b8b519de696b7e63dd1)`

This way you know for sure that your change was captured...without you having to do anything special at all.

## Reviewing Changes

Once a change has been logged, you can report on the change by running:

`ver 'spTest'`

This will return a resultset containing information about the most recent change:

+----------------+-----------------------+--------------+--------+---------------------------------------------------------------------------------------------------------------------+-----------------------+-----------+------------------------------------------+
|Object          |LastUpdate             |LastUpdateBy  |Comments|SQLCommand                                                                                                           |DateAppeared           |SchemalogID|Hash                                      |
+----------------+-----------------------+--------------+--------+---------------------------------------------------------------------------------------------------------------------+-----------------------+-----------+------------------------------------------+
|osDev.dbo.spTest|2019-01-09 20:09:18.577|opsstreamadmin|NULL    |ALTER PROCEDURE dbo.spTest  {{!AS!}}  BEGIN    SELECT     GETDATE() AS CurrentTime,     'Hello World' AS Hello    END|2019-01-09 20:06:55.760|2610       |0x00B03DEBE66373A500FC64EC6978462AC84876B0|
+----------------+-----------------------+--------------+--------+---------------------------------------------------------------------------------------------------------------------+-----------------------+-----------+------------------------------------------+

If you want to see additional versions, you can specify the maximum number of versions you want to return like this:

`ver 'spTest', 10`

This returns a resultset with a row for the N most recent versions:

+---------------------+-----------------------+--------------+--------+---------------------------------------------------------------------------------------------------------------------+-----------------------+-----------+------------------------------------------+
|Object               |LastUpdate             |LastUpdateBy  |Comments|SQLCommand                                                                                                           |DateAppeared           |SchemaLogID|Hash                                      |
+---------------------+-----------------------+--------------+--------+---------------------------------------------------------------------------------------------------------------------+-----------------------+-----------+------------------------------------------+
|osDev.dbo.spTest     |2019-01-09 20:09:18.577|opsstreamadmin|NULL    |ALTER PROCEDURE dbo.spTest  {{!AS!}}  BEGIN    SELECT     GETDATE() AS CurrentTime,     'Hello World' AS Hello    END|2019-01-09 20:06:55.760|2610       |0x00B03DEBE66373A500FC64EC6978462AC84876B0|
+---------------------+-----------------------+--------------+--------+---------------------------------------------------------------------------------------------------------------------+-----------------------+-----------+------------------------------------------+
|OSAGRI_Dev.dbo.spTest|2019-01-09 20:06:55.760|opsstreamadmin|NULL    |CREATE PROCEDURE dbo.spTest  {{!AS!}}  BEGIN    SELECT GETDATE() AS CurrentTime  END                                 |2019-01-09 20:06:55.760|2609       |0x2D072B4A4999D767972B3B8B519DE696B7E63DD1|
+---------------------+-----------------------+--------------+--------+---------------------------------------------------------------------------------------------------------------------+-----------------------+-----------+------------------------------------------+

## Reverting to Earlier Version

Once you reviewed these results, suppose you wanted to revert back to an earlier version.  You could specify a specific version, like this:

`ver 'spTest', @SchemaLogID = 2609`

In addition to returning the specified version in a single-row resultset, the "Messages" tab in SSMS will also show the complete text of the command for that version:

```
ALTER PROCEDURE dbo.spTest
AS
BEGIN
  SELECT GETDATE() AS CurrentTime
END~
```

This command is ready to copy, paste into your editor, make a few minor changes, and execute.  (Notice how the command for version 2609 is stored with "CREATE PROCEDURE", but SQLVer automatically outputs this as "ALTER PROCEDURE" for your convenience.)

The minor changes are to review the command, and delete the tilde \~ character and the following newline break.

*The reason the tilde characters are present:  SSMS has a maximum string length to what you an PRINT to the Messages tab.  SQLVer takes care of breaking the statement into chunks that are printed individually...thereby allowing a string of any length to be printed.  The \~ followed by the new line marks the end of each "chunk", and must be removed prior to executing the code.*

Note that the "ver" command is really shorthand for:

`EXEC sqlver.spVersion`

That is, there is a SQLVer stored procedure named sqlver.spVersion that has a synonym "ver" defined.  If the statement is the first statement in a batch, the EXEC is optional

## Searching

SQLVer also provides a simple way to search for a string anywhere in the database module definitions (source code for stored procedures, functions and views).

Suppose you wanted to search for the word 'test':

`find 'test'`

This returns a list of all the objects in which that string was found:

+----------+-----------------------+------------------------------------------------------------------------------------+
|SchemaName|ObjectName             |Context                                                                             |
+----------+-----------------------+------------------------------------------------------------------------------------+
|dbo       |spTest                 |CREATE PROCEDURE dbo.spTest  --$!ParseMa...                                         |
+----------+-----------------------+------------------------------------------------------------------------------------+
|sqlver    |spsysBuildCLR_WordMerge|gnature.    --Extensive trial-and-error testing on 12/30/2015 did not reveal a bette|
+----------+-----------------------+------------------------------------------------------------------------------------+
|...       |                       |                                                                                    |
+----------+-----------------------+------------------------------------------------------------------------------------+

How cool is that!