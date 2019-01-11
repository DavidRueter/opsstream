OpsStream runs on Microsoft SQL Server (MSSQL).

OpsStream requires at least SQL 2008R2, but some features require more modern versions (SQL 20016 or later).

OpsStream will run on any edition of SQL, from the free SQL Express up to SQL Enterprise edition.

Many applications and platforms consider the database to be a place "just" to store data, and are built in a way to allow changing the database platform (from MSSQL to Oracle for example).  OpsStream is not such an application.

MSSQL is incredibly powerful and flexible, and OpsStream fully uses this capability.  The OpsStream core is actually written in T-SQL and is implemented as stored procedures, user-defined functions, and views.  OpsStream treats SQL more as an application server than as "just" a data storage platform.

The server-side footprint of OpsStream is pretty small.  From SQL OpsStream really uses only the database engine.  It does use SQL Agent to run a single recurring job every 5 minutes.  (If using SQL Express, this job needs to be executed in a different way.)   OpsStream does not requires SSRS, SSIS, or data analytics support...but you are free to use these components if you so choose.

Some features of OpsStream do require [SQL CLR (.NET)](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/clr-enabled-server-configuration-option?view=sql-server-2017) support.  We recommend that you leave CLR support enabled.  Also, some features do require [OLE Automation procedures](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/ole-automation-stored-procedures-transact-sql?view=sql-server-2017).  Again, we recommend that you leave OLE Automation support enabled.  However, if either of these is a deal-breaker, OpsStream will run without either CLR or OLE Automation procedures--though some features will be unavailable.

In some configure-time stored procedures (as opposed to run-time), OpsStream makes temporary use of [xp_cmdshell](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option?view=sql-server-2017).  In the rare cases when xp_cmdshell is temporarily needed, OpsStream will enable it, perform the needed task, and then revert the option setting back to the way it was.  In other words, you can (and should) leave xp_cmdshell disabled, but there are a few times when OpsStream will briefly enable it to perform a specific configuration task.

OpsStream intentionally encapsulates the entire OpsStream core within a single database that is used for a single OpsStream instance.  (An OpsStream instances is not a multi-tenant application:  each instance has its own database.)  Among other things, this facilitates scalability and portability:  it is easy to backup an OpsStream database, and then restore it on a different server.  All configuration settings, data, and functionality are all within the database with few external dependencies.

Note:  The OpsStream user interface is provided by a self-contained web application server.  There are two versions of this UI server:  both are supported, and these can be run concurrently.  The legacy UI server is a compiled Win32 service.  The modern UI server is [Theas](https://github.com/davidrueter/Theas) (a self-contained web application platform we created in Python and released as open source), which can be deployed either as a Windows service or run on Linux.

Both of these web application servers ONLY provide UI and web service hosting.  All functionality, and even all screens and UX definitions are stored in SQL.  Likewise, all business logic and custom code is in SQL.  There is no other place where OpsStream code exists:  there is no .NET or Java application.  Everything is in SQL.