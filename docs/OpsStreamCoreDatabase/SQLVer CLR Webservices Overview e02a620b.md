There are some things that I wanted to be able to do in SQL, such as generate a PDF document or resize a JPEG, that pushed the envelope of what SQL CLR was designed to do.

SQL CLR is really meant to be used with only specific assemblies.  Many assemblies are "unsupported" or "unsafe".  But still, some of these assemblies (such as System.Drawing) worked fine in SQL2008R2 (.NET 3.5).  Later, however, in SQL 2012 (.NET 4.0) some of these assemblies stopped working (because some methods were switched from CIL to native code...which is forbidden under SQL CLR).

In any case, some things are simply not possible (let alone prudent) to do in SQL CLR.

But if I really want to have a stored procedure that generates a PDF document and stores that resulting document, or that takes a stored image and generates a thumbnail, what should I do?  Sure, I could create an external app...but that kind of goes against the principle of encapsulating everything in the database.

What I ended up doing was to create a stand-alone self-contained web service server in C#.  This can run as a Windows service, either on the same machine that SQL runs on, or a different machine.  This web service server is for internal use only:  NO outside browser or web service client should EVER be able to make requests to this server.  (If you need to support remote requests, use Theas or the legacy OpsStream UI server.)

I then migrated the C# code for certain assemblies (PDF generation, image resizing, and potentially other things in the future) to this stand-alone web service server.

Now I can use SQLVer's GetHTTP capabilities to pass data (i.e. in XML) to a web service, and receive a reply containing the binary data (i.e. PDF or JPEG) that I want to store.

So this SQLVer CLR Webservice Server is an optional component of SQLCLR.  You can use it if you wish to, or not:  the choice is yours.