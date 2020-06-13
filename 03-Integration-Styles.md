# Integration Styles

Enterprise integration is the task of making separate applications work together to produce a unified set of functionality. Some applications may be custom developed in-house while others are bought from third-party vendors. The applications probably run on multiple computers, which may represent multiple platforms, and may be geographically dispersed. Some of the applications may be run outside of the enterprise by business partners or customers. Some applications may need to be integrated even though they were not designed for integration and cannot be changed. These issues and others like them are what make application integration difficult. This chapter will explore the options available for application integration.

## Application Integration Criteria
There are several different criteria (listed below) that must be considered when choosing and designing an integration approach. The question then becomes: Which integration approaches best address which of these criteria?

**Application coupling** — Even integrated applications should minimize their dependencies on each other so that each can evolve without causing problems for the others. The interface for integrating applications should be specific enough to implement useful functionality, but general enough to allow that implementation to change as needed.

**Integration simplicity** — When integrating an application into an enterprise, developers should strive to minimize changing the application and minimize the amount of integration code needed.

**Integration technology** — Different integration techniques require varying amounts of specialized software and hardware. These special tools can be expensive, can lead to vendor lock-in, and increase the burden on developers to understand how to use the tools to integrate applications.

**Data format** — Integrated applications must agree on the format of the data they exchange, or must have an intermediate traslator to unify applications that insist on different data formats. A related issue is **data format evolution and extensibility** — how the format can change over time and how that will affect the applications.

**Data timeliness** — Integration should minimize the length of time between when one application decides to share some data and other applications have that data. Data should be exchanged frequently in small chunks, rather than waiting to exchange a large set of unrelated items. Applications should be informed as soon as shared data is ready for consumption. Latency in data sharing has to be factored into the integration design; the longer sharing can take, the more opportunity for shared data to become stale, and the more complex integration becomes.

**Data or functionality** — Integrated applications may not want to simply share data, they may wish to share functionality such that each application can invoke the functionality in the others. Invoking functionality remotely can be difficult to achieve, and even though it may seem the same as invoking local functionality, it works quite differently, with significant consequences for how well the integration works.

**Asynchronicity** — Computer processing is typically synchronous, such that a procedure waits while its subprocedure executes. It’s a given that the subprocedure is available when the procedure wants to invoke it. However, a procedure may not want to wait for the subprocedure to execute; it may want to invoke the subprocedure asynchronously, starting the subprocedure but then letting it execute in the background. This is especially true of integrated applications, where the remote application may not be running or the network may be unavailable—the source application may wish to simply make shared data available or log a request for a subprocedure call, but then go on to other work confident that the remote application will act sometime later.

## Application Integration Options
Each approach addresses some of the integration criteria better than others. The various approaches can be summed up in four main integration styles:

**File Transfer** — Have each application produce files of shared data for others to consume, and consume files that others have produced.

**Shared Database** — Have the applications store the data they wish to share in a common database.

**Remote Procedure Invocation** — Have each application expose some of its procedures so that they can be invoked remotely, and have applications invoke those to run behavior and exchange data.

**Messaging** — Have each application connect to a common messaging system, and exchange data and invoke behavior using messages.

Each pattern builds on the last, looking for a more sophisticated approach to address the shortcomings of its predecessors. Thus the pattern order reflects an increasing order of sophistication.

Two applications may integrate using multiple styles such that each point of integration takes advantage of the style that suits it best. We focus on messaging in part because we believe it is often the best style for solving many integration opportunities. It is also ripe with patterns that quickly explain how to make good use of it.

### File Transfer
Multiple pieces of software handle different aspects of the enterprise. This is due to a host of reasons.
* People buy packages that are developed by outside organizations
* Different system are built at different times, leading to different technology choices
* Different systems are built by different people, whose experience and preferences lead them to different approaches to building applications

As a result, any organization has to worry about sharing information between very divergent applications. These can be written in different languages, based on different platforms, and with some different assumptions about how the business operates.

Tying such applications together requires a lot of knowledge of understanding how to link together applications on both a business level and a technical level. To have any chance of getting your head around it, you must minimize what you need to know about how each application works.

Files are a universal storage mechanism, built in to any enterprise operating system, available from any enterprise language. The simplest approach would be to somehow integrate the applications using files.

Have each application produce files containing information that other applications need to consume. Integrators take the responsibility of transforming files into different formats. Produce the files at regular intervals according to the nature of the business.

Mainframe systems commonly use data feeds based on the file system formats of COBOL. Unix systems use text based files. The modern fashion is to use XML.

Another issue with files is when to produce them and consume them. Typically you have some regular business cycle that drives the decision: nightly, weekly, quarterly, etc.

The great advantage of files is that integrators need no knowledge of the internals of an application. As a result the different applications are quite nicely decoupled from each other. The files effectively become the interface of each application.

Part of what makes File Transfer simple is that no extra tools or integration packages are needed, but that also means that developers have to do a lot of the work themselves. The applications must agree on file naming conventions and the directories they appear in. The writer of a file must implement a strategy to keep the filenames unique. The applications must agree on which one will delete old files, and that application will have to know when a file is old and no longer needed. The applications will need to implement a locking mechanism or follow a timing convention to ensure that one application is not trying to read the file while another is still writing it. If all of the applications do not have access to the same disk, then some application must take responsibility for transferring the file from one disk to another.

One of the most obvious issues with File Transfer is that updates tend to occur infrequently, as a result systems can get out of synchronization. Sometimes lack of synchronization isn't a big deal. People often expect a certain lag in getting information around, even with computers. At other times the result of using stale information is a disaster. When deciding on when to produce files, you have to take the freshness needs of consumers into account.

Indeed you can think of Messaging as File Transfer where you produce a file with every change in an application. The problem then is managing all the files that get produced, ensuring they are all read and none get lost. This goes beyond what file system based approaches can do, particularly since there are expensive resource costs with processing a file, which get prohibitive if you want to produce lots of files quickly. As a result, once you get to fine grained files like this, it's easier to think of them as Messaging.

To make data available more quickly and enforce an agreed-upon set of data formats, use a Shared Database. To integrate applications' functionality rather than their data, use Remote Procedure Invocation. To enable frequent exchanges of small amounts of data, perhaps used to invoke remote functionality, use Messaging.

### Shared Database
For modern businesses, you want everyone to have the latest data as much as possible. Not just does this reduce errors, it also increases people's trust in the data.

Rapid updates also allow inconsistencies to be handled better. The more frequently you synchronize, the less likely you are to get inconsistencies and the less effort they are to deal with. If a family of integrated applications all rely on the same database, then you can be pretty sure that they are always consistent all of the time. If you do get simultaneous updates to a single piece of data from different sources, then you have transaction management systems that handle that about as gracefully as it ever can be managed.

Since everyone is using the same database, this forces out problems in *semantic dissonance*. Rather than leaving these problems to fester until they are difficult to solve with transforms, you are forced to confront them and deal with them before the software goes live and you collect large amounts of incompatible data.

One of the biggest difficulties with Shared Database is coming up with a suitable design for the shared database. Coming up with a unified schema that can meet the needs of multiple applications is a very difficult exercise, often resulting in a schema that application programmers find difficult to work with.

Multiple applications using a Shared Database to frequently read and modify the same data can cause performance bottlenecks and even deadlocks as each application locks others out of the data. When the applications are distributed across multiple computers, the database must be distributed as well so that each application can access the database locally, which confuses the issue of which computer the data should be stored on. A distributed database with locking conflicts can easily become a performance nightmare.

To enable frequent exchanges of small amounts of data, using a format per datatype rather than one universal schema, use Messaging.

### Remote Procedure Invocation
Shared Database provides a large, unencapsulated data structure. Many changes in any application can trigger a change in the database, and database changes have a considerable ripple effect through every application. As a result, systems that use Shared Database are often very reluctant to change the database, which means that the application development work is much less responsive to the changing needs of the business.

What is needed is a mechanism for one application to invoke a function in another application, passing the data that needs to be shared and invoking the function that tells the receiver application how to process the data.

Remote Procedure Invocation applies the principle of encapsulation to integrating applications. If an application needs some information that is owned by another application, it asks that application directly. If one application needs to modify the data of another, then it does so by making a call to the other application. Each application can maintain the integrity of the data it owns. Furthermore, each application can alter its internal data without having every other application be affected.

There are a number of Remote Procedure Call (RPC) approaches: CORBA, COM, .NET Remoting, Java RMI, etc. For sheer ubiquity, the current fashionable favorite is Web Services using standards such as SOAP and XML. A particularly valuable feature of web services is that they work easily with HTTP, which is easy to get through firewalls.

The fact that there are methods that wrap the data make it easier to deal with semantic dissonance. Applications can provide multiple interfaces to the same data, allowing some clients to see one style and others another. Even updates can use multiple interfaces. This provides a lot more ability to support multiple points of view than relational views.

Since software developers are used to procedure calls, Remote Procedure Invocation fits in nicely with what they are already used to. Actually, this is more of a disadvantage than it is an advantage. There are big differences in performance and reliability between remote and local procedure calls. If people don't understand these, then Remote Procedure Invocation can lead to slow and unreliable systems.

Although the encapsulation helps reduce the coupling of the applications, by eliminating a large shared data structure, the applications are still fairly tightly coupled together. The remote calls each system supports tends to tie the different systems into a growing knot. In particular, sequencing--doing certain things in a particular order--can make it difficult to change systems independently. Often these become problems because issues that aren't significant within a single application become so when integrating applications. People often design the integration the way they would design a single application, unaware that the rules change.

### Messaging
Often the challenge of integration is about making collaboration between separate systems as timely as possible, without coupling systems together in such a way that makes them unreliable, either in terms of application execution or application development.

Remote Procedure Invocation extends model used for a single application to application integration can run into plenty of weaknesses. These weaknesses start with the essential problems of distributed development. Despite the fact that remote procedure calls look like local calls, they don't act the same. Remote calls are slower, and can fail. With multiple applications communicating across an enterprise, you don't want one application's failure to bring down all of the other applications.

What we need is something like File Transfer where lots of little data packets can be produced quickly, transferred easily, and the receiver application is automatically notified when a new packet is available for consumption. The transfer needs a retry mechanism to make sure it succeeds. The details of any disk structure or database for storing the data needs to be hidden from the applications so that, unlike Shared Database, the storage schema and details can be easily changed to reflect the changing needs of the enterprise. One application should be able to send a packet of data to another application to invoke behavior in the other application, like Remote Procedure Invocation, but without being prone to failure. The data transfer should be asynchronous so that the sender does not need to wait on the receiver, especially when retry is necessary.

Asynchronous messaging is fundamentally a pragmatic reaction to the problems of distributed systems. Sending a message does not require both systems to be up and ready at the same time. Furthermore, thinking about the communication in an asynchronous manner forces developers to recognize that working with a remote application is slower, which encourages design of components with high cohesion (lots of work locally) and low adhesion (selective work remotely).

Messaging systems also allow much of the decoupling you get when using File Transfer. Messages can be transformed in transit without either the sender or receiver knowing about the transformation.

The transformation means that separate applications can have quite different conceptual models. Of course this means that semantic dissonance will occur, but the messaging viewpoint is the measure that Shared Database takes to avoid semantic dissonance are too complicated to work in practice.

By sending small messages frequently, you also allow applications to collaborate behaviorally as well as share data. If a process needs to be launched once an insurance claim is received, it can be done immediately as a message when a single claim comes in. While such collaboration isn't going to be as fast as Remote Procedure Invocation, the caller needn't stop while the message is being processed and the response returned. And messaging isn't as slow as many people think -- many messaging solutions originated in the financial services industry where thousands of stock quotes or trades have to pass through a messaging system every second.

The high frequency of messages in Messaging reduces many of the inconsistency problems that bedevil File Transfer, but it doesn't entirely remove them. There is still going to be some lag problems with systems not being updated quite simultaneously.

Asynchronous design is not the way most software people are taught, and as a result there's a whole host of different rules and techniques in place. The messaging context makes this a bit easier than programming in a asynchronous application environment like X windows, but asynchrony still has a learning curve. Testing and debugging are also harder in this environment.
