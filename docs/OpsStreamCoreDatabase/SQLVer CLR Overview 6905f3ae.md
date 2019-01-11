[SQL Server CLR](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/introduction-to-sql-server-clr-integration) is part of Microsoft SQL.  This allows you to create CLR assemblies that are stored in the database.  You can then call stored procedures and/or functions that map to methods in these assemblies.

SQL CLR allows you to write code C# (or VB.NET or another .NET-supported language), and have this code be executed by a SQL stored procedure, function, trigger or query.

This capability opens up all kinds of possibilities.  But with great power comes great responsibility:  you must use SQL CLR wisely if you use it at all.  When used improperly, SQL CLR can compromise performance, security, and server stability.

Some people are opposed to using SQL CLR at all.  Some people use SQL CLR improperly or too much.  But there is middle ground in which SQL CLR can be used responsibly, with safety and stability.

SQLVer provides a framework for using SQL CLR that simplifies its use, and safeguards against common mistakes and lazy shortcuts that compromise security and performance.  SQLVer also provides a handful of pre-built CLR assemblies that are ready to use.  One important example is the GetHTTP assembly that allows fetching of data from remote web services and web pages.

SQLVer's approach lets you copy your C# source code into a stored procedure (as a string literal), and then lets you run a SQLVer procedure that compiles and builds this into a CLR assembly, registers the assembly in the SQL database, and creates wrapper stored procedures and/or functions to call methods in the assembly.

That's right:  with SQLVer you can create and deploy a SQL CLR assembly all within SQL...without even using Visual Studio!

This supports our notion of "encapsulation"where everything related to the functionality of OpsStream is stored within the database with no outside dependencies.  This also allows a SQL-fluent developer or DBA to make changes to (or even create) SQL CLR assemblies without needing to work with a C# developer or even fire up Visual Studio.