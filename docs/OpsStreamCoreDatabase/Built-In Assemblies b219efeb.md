SQLVer supports you in creating and deploying your own CLR assemblies.  But SQLVer also ships with some handy ready-to-use CLR assemblies.

## GetHTTP

The most important CLR assembly that is part of SQLVer is GetHTTP.  This allows you to make HTTP requests (both GET and POST requests) to retrieve and send data from/to remote web services and web sites.

GetHTTP supports cookies, custom headers, mutipart binary posts, retrieving binary data, SSL, user/password basic authentication, and automatic following of redirects.

The GetHTTP assembly also provides methods for URL-encoding and decoding ("percent encoding").

To build and deploy this assembly, execute `sqlver.sysBuildCLR_GetHTTP`

You can look at the source code of `sqlver.spsysBuildCLR_GetHTTP` to see exactly how it is implemented.  This is a good reference for how to create additional assemblies.

## SendMail

The SendMail assembly lets you send emails directly from SQL (without using SQL's built-in SQLMail).

SendMail supports HTML and text email bodies, as well as attachments.  It requires no  special configuration or permissions in SQL.

To build and deploy this assembly, execute `sqlver.spsys_SendMail`

## FTP

The FTP assembly lets you upload and download files from a remote FTP server.

To build and deploy this assembly, execute `sqlver.spsysBuildCLR_FTP`

SQLVerUtil

The SQLVerUtil is an assembly to hold miscellaneous  other utilities.

For example, this assembly contains a method GetMP3Info_CLR that allows you to run a SQL function to retrieve metadata from MP3 binary data stored in the database.

(That sounds crazy...but actually if you have a use-case where OpsStream needs to manage MP3 content, this is really useful.)

## Obsolete

SQLVer previously included these CLR assemblies:

* PDFCLR:  A wrapper around [itextsharp](https://github.com/itext/itextsharp) which is a library for creating PDF documents.  This allows you to pass XML into a SQL function to create a PDF document from scratch, or to add (overlay) text to an existing PDF document.  This still works, but SQLVer now provides a more robust way to create PDF documents.  See this [article](http://www.sqlservercentral.com/articles/pdf/98193/) that I wrote some time ago that describes this approach in detail. 
* ImageTools:  Provides a method to resize JPEG images.  Handy for creating thumbnails of images stored in the database.  Does not work on SQL 2012 or later (due to changes in .NET)  But this method is now available as a SQLVer CLR WebService.
* WordMerge:  Provides a way to merge data into a Microsoft Word mail merge template and return the result as a PDF.  Does not work on SQL 2012 or later (due to changes in .NET)  But this method is now available as a SQLVer CLR WebService.