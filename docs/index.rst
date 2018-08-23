#####################
﻿Introduction to OpsStream
#####################


Service Broker
**************
OpsStream can optionally make use of Service Broker for:

    #. Asynchronously handling events within SQL
    #. Asynchronously handling events outside of SQL

OpsStream's event handling system includes a central event log (opsstream.tblSysLog).  Events that are logged here are defined in SysEventTypes (opsstream.tblSysEventTypes).  Log entries can be written by calling `opsstream.spsysEventRaise`_ and passing in either @SysEventTypeID or @SysEventTypeCode, along with other event-specific information to write to the log.

But beyond merely writing events to opsstream.tblSysLog, event types can be configured to also insert a messsage into a Service Broker queue.

For example, suppose that you need an event that is raised when data changes.  The event handler must generate a document from the updated data, and send that generated document via email.

To accomplish this, you could create a new event type (see `opsstream.spinsSysEventTypes`_)  After creating this event type, you could then create corresponding Service Broker support (see `opsstream.spinsSysEventTypeSB`_) and specify @SBRootName and other related parameters.  This will result in the automatic creation of all needed Service Broker objects:  queues, message types, contracts, services routes, and permissions.  All of these will be sensibly named, using the string provided in @SBRootName as the base of the name.

Then, when `opsstream.spsysEventRaise`_ is called, the event will be logged to SysEventTypes, AND a message will be sent to the related Service Broker queue.

Messages in the Service Broker queue can either be handled by a stored procedure within SQL (internal activation), or by an external application.

Regardless of the endpoint, OpsStream automatically generates sensible objects, stored procedures and other aspects of the Service Broker configuration to easily support simple sending and receiving of messages through a queue.  Messages will be sent to the queue automatically when the event is raised.

OpsStream could also be set up to receive (rather than send) messages.  Some of the architecture and stored procedures in OpsStream can help facilitate that, but this scenario is not a built-in capability in the way that sending of messages from OpsStream is.

Managing Service Broker with Stored Procedures
**************

Though Service Broker is incredibly powerful, setting it all up by hand is somewhat painful:  for even simple use of Service Broker, each queue requires that more than a dozen individual SQL objects be created and configured.

OpsStream provides stored procedures to perform this configuration and de-configuration from a single stored procedure call.  OpsStream also provides stored procedures to manage Service Broker, doing things like:  purging messages from a queue, resetting a queue, enabling or disabling Service Broker, resetting service broker, re-creating and removing Service Broker objects, and more.

`opsstream.spinsSysEventTypesSB`_  Used to turn a SysEventType into a Service Broker-enabled event.  Automatically enables Service Broker if needed, and creates all needed Service Broker objects.

`opsstream.spdelSysEventTypesSB`_  Used to remove Service Broker capabilities from a SysEventType.  Automatically drops unneeded Service Broker objects.

`opsstream.spsysServiceBrokerObjectsCreate`_  Called internally by `opsstream.spinsSysEventTypesSB`_ to actually create Service Broker objects for a particular root name (@SBRootName).  Can also be used to create arbitrary sets of Service Broker objects that are not managed by OpsStream.

`opsstream.spsysServiceBrokerObjectsDrop`_  Called internally by `opsstream.spdelSysEventTypesSB`_ to actually drop Service Broker objects for a particular root name (@SBRootName).  Can also be used to drop arbitrary sets of Service Broker objects that are not managed by OpsStream.

`opsstream.spsysServiceBrokerPurgeQueues`_  Can be called to purge messages from a specified queue, or from all queues managed by OpsStream.

`opsstream.spsysServiceBrokerReset`_  Can be called to reset Service Broker.  Defaults to clearing messages from the queue(s) (@ClearMessages=1), but can be set to @ClearMessages=0.  Defaults to @HardReset = 0 to only, which stops and resets the queue(s) by ALTER QUEUE xxx WITH STATUS = OFF / ON.  Can be set to @HardReset = 1 to forcibly drop all Service Broker objects, disable Service Broker, re-create all Service Broker objects, and re-enable Service Broker.

`opsstream.spsysServiceBrokerDisable`_  Called by `opsstream.spsysServiceBrokerReset`_ if @HardReset = 1 but can also be called directly to drop all Service Broker objects, save a backup of their definitions, and disable Service Broker.

`opsstream.spsysServiceBrokerEnable`_  Called by `opsstream.spsysServiceBrokerReset`_ if @HardReset = 1 but can also be called directly to re-create all Service Broker objects as defined in opsstream.tblSysEventTypesSB and/or the backup copy of those definitions that was made automatically by `opsstream.spsysServiceBrokerDisable`_.

`opsstream.spsysOnSBActivate#xxx_RaiseResp`_  Created automatically by the system (in `opsstream.spsysServiceBrokerObjectsCreate`_) as an internal activation stored procedure for automatically sending a response to messages received.  Rather than using a "fire-and-forget" pattern for messages, which is inadequate to guarantee delivery, the default OpsStream configuration automatically sends an explicit response to every message.  That response message will contain error information if the message could not be processed properly, or will otherwise contain an acknowledgement of delivery of the message.  This all works fine out-of-the-box with no manual configuration.  But if you want to take explicit control of the message responses, you can do so by altering or replacing this stored procedure.

optional activation stored procedure, passed into opsstream.spinsSysEventTypesSB  If messages sent to the queue are to be processed within the OpsStream SQL database, you can create an internal activation stored procedure and pass the name of that procedure in when the Service Broker capabilities of the event type are created.  OpsStream ships with one such activation stored procedure:  `opsstream.spOnActivate_BCPrint`_ which is used to generate barcode label data prior to sending it to a label printer.  You can copy-and-paste this procedure defintion to use as a template for creating your own internal activation stored procedure.  (If you do not define an internal activation stored procedure, messages will not be automatically processed.  Instead, messages will remain in the queue until an external application explicitly retrieves the messages from the queue.)

`opsstream.spsysEventsSBHandle`_  If not using an internal activation stored procedure, and instead having an external application processe enqueued messages, the external application can call this stored procedure to retrieve messages from the queue.

`opsstream.spsysEventSBReply`_  If not using an internal activation stored procedure, and instead having an external application processe enqueued messages, the external application can call this stored procedure to send responses to messages retrieved from the queue.

Troubleshooting
**********************

Service Broker runs reliably.  However certain things can happen to disrupt Service Broker.  (This has not actually happened for many years, but under SQL2005 it seemed to happen from time to time.)

If something goes wrong, symptoms may vary.  Sometimes messages cannot be delivered to a queue.  Sometimes messages cannot be retrieved from a queue.  Sometimes the activation stored procedure cannot be called.  And so on...

Getting Service Broker back on track can seem like a daunting task, because there are so many different objects to consider, and because most of us don't do deep work with Service Broker frequently...making it challenging to remember how everything fits together.

The following steps can be followed to get Service Broker working again in the event that a problem arises.

    #. EXEC opsstream.spsysServiceBrokerReset @HardReset = 0, @ClearMessages = 0
    Minimally invasive.  Simply stops and restarts each queue.  Will not delete any messages, and will not disrupt any users or any aspects of the system.
    
    #. EXEC opsstream.spsysServiceBrokerReset @HardReset = 0, @ClearMessages = 1
    Will clear out any messages in the queue.  This means that the data in these messages will be permanently LOST.  If the nature of the messages is something like a barcode label print job that can be re-printed, clearing messages is not a big deal.  (If the nature of the messages is more like posting bank account transactions...do not clear messages without understanding the implications of doing so.)
    
    #. EXEC opsstream.spsysServiceBrokerReset @HardReset = 1, @ClearMessages = 1
    Will impact all users on the system.  Will drop all user SQL connections, will momentarily put the database in SINGLE_USER mode, will drop all Service Broker objects, will disable Service Broker.  Will then re-create / re-enable Service Broker, will re-create all Service Broker objects, and will bring the databaswe back to MULTI_USER mode.  Don't do this unnecessarily, as it is disruptive.  But this can be done safely as far as Service Broker-related objects are concerned:  everything will be automatically recreated.
    
    #. EXEC opsstream.spsysServiceBrokerEnable
    If for some reason opsstream.spsysServiceBrokerReset ends in an error (unlikely), and the Service Broker objects are not re-created, you can directly run this stored procedure to fix things.  This will first drop all Service Broker objects and will then re-create these based on the definitions in the backup of SysEventTypesSB that was automatically made at the start of opsstream.spSysServiceBrokerReset (actually, in opsstream.spsysServiceBrokerDisable that is called by opsstream.spsysServiceBrokerReset), and/or based on the definitions still in the live opsstream.tblSysEventTypesSB table.
    
    #. EXEC opsstream.spsysServiceBrokerDisable
    If for some reason you want to drop all Service Broker objects and disable Service Broker...without immediately recreating objects and re-enabling Service Broker, you can call this stored procedure.  You can subsequently call opsstream.spsysServiceBrokerEnable to re-create and re-enable everything.
    
    #. Check for error messages logged in either opsstream.tblSysRTMessages or opsstream.tblSysLog.  Check external application log files.  Consider enabling @Debug = 1 flags inside activation stored procedures.

