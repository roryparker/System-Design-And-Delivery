# System Design and Delivery

This is study materials for System Design and why it is important to new and current Software Engineers.

Four Categories of Requirements Clarification - During requirements clarification, the interviewer starts to understand your level of expertise in systems design.

Users - Understand who and how will use the system.
* Help us understand what data we store in the system.

Scale - Understand how our system will handle a growing amount of data.
* How much data is retrieved from the system.
* How much data is coming to the system.
* How many (texts, photos, videos, views) happen per second are processed by the system.
* How much data is queried per request
* How many read queries per second the system needs to process
Should we deal with sudden traffic spikes and how big they may be.
Interviewer will help us define these numbers OR we may assume some reasonable values.
Performance - We want to know how fast our system must be.
Help us quickly evaluate different design options
Cost - Understand budget constraints.
As with coding interviews, if you do not ask questions, this is a warning sign for the interviewer.
Helps us evaluate technology stack.
Minimize development cost by leaning towards well-regarded open-source frameworks.
Future maintenance cost is a primary concern, consider public cloud services and automation for our design.

Interviewer will ask vague design questions to see how you deal with ambiguity
Problems will be stated in a general manner.
Solutions stated in generic form.
System Design questions are usually open-ended.
Can identify key pieces of the system and define a scope of the problem.
These questions are asked to understand how you approach design problems in real life.
How you respond to the task and actions.

Questions to ask interviewer: 
Confirm what functional pieces of the problem we will focus on
Clarify the requirements because there are so many different technology solutions.
What does data analysis mean?
How will the data be used?
Who sends us data?
Site visitors, partners, app users, etc.
Who uses the results of this analysis?
Users, employees(departments), government, etc.
What does real-time really mean?
Near real time or minutes after

When designing solutions
Pick the option that addresses requirements
During requirements clarification interviewer starts to understand your level of expertise

High Level Architecture
Place components on the whiteboard (Database, Processing Server, Query Service, etc.)



Do not discuss components just yet due to architecture setup (things may change). Once you are comfortable with the pieces, then talk about it in detail. The end goal of requirements clarification discussions is to get us closer to defining both functional and non-functional requirements. After figuring out what the system will do, write it down on the whiteboard in a few sentences.





























Typical system design interview (SDI)
We usually do not need to know the architectures of different databases.
Know advantages and disadvantages of each and when to use what.
Don't think that all NoSQL databases have similar architectures.
Don't think that all SQL databases have similar architectures.


Setup and create functions related to the system design example.

Functional Requirements - 
System behavior, or more specifically APIs
Set of operations the system will support.

Parameters - 
Indicate what event we will process


Non-Functional Requirements
System qualities, such as fast, fault-tolerant, secure.





Define a Data Model

Do we store the individual event?
When we store individual events we capture all attributes of the event: video identifier, timestamp, user related information such as country, device type, operating system and
Advantages: 
Individual events can be stored really fast.
We just get the event and push it to the database.
When retrieving data, we can slice and dice data however we want.
We can filter based on specific attributes, aggregate based on some rules.
If there was a bug in some business report, we can recalculate numbers from scratch.
Disadvantages:
We cannot read data quickly.
We need to count each individual event when total count is requested. This takes time.
It may cost a lot of money to store all the raw events.

Do we calculate actions on the fly and store the aggregated data? 
When we aggregate data we calculate a total count per time interval, let's say one minute and we lose details of each individual event.
Advantages:
Reads become fast when we aggregate data.
We do not need to calculate each individual event, we just retrieve the total count value.
We can use it for decision making in real-time. Send the values to a recommendation service or trending service
Disadvantages:
We can only query data the way it was aggregated.
Ability to filter data or aggregate it differently is very limited.
Requires us to implement a data aggregation pipeline. We need to somehow pre-aggregate data in memory before storing it in the database.
Difficult or even impossible to fix errors.








Store raw events or aggregate data in real-time?
This is where we need the interviewer to help us make a decision.
We should ask the interviewer:
What is the expected data delay?
Time between when the event happened and when it was processed.
If it should be no more than several minutes - we must aggregate data on the fly.
If several hours is ok, then we can store raw events and process them in the background.
Former approach is also known as stream data processing, while the latter is called batch data processing.
The interviewer will let us know what option she is interested in the most.

In some instances, you can pick both options.
Combining both approaches makes a lot of sense for many systems out there.
Introduces complexity and flexibility.


































Evaluate databases based on non-functional requirements:
Scalability - expand up, down, or sideways quickly.
Performance - No downtime, max throughput, community troubleshooting.
Availability - Prefers uptime; sacrifices consistency (show stale/old data vs. data unity
Consistency - Shows data in uniformity
Database solution we choose:
Should scale well for both reads and writes.
It should be fast and guarantee high availability.
Achieve the required level of data consistency.
Understand how to recover data, achieve security, and apply future data models changes.
Pick hardware and evaluate the cost of the solution.

Synchronous Data Replication is slow, we usually replicate data asynchronously.
Eventual Consistency - Temporary process where over time writes will propagate to replicas so replicas are not behind the master.







Relation DB Design
Define nouns(person, place, thing) in the system
Convert nouns in tables and use foreign keys to reference related data in these tables.
Implement DB Normalization (minimize data duplication across different tables)

Simple to store all our data on a single database machine.
When a single machine is not enough, we need to introduce more machines and split data between them.
This procedure is called sharding or horizontal partitioning.
Each shard stores a subset of all the data.

Can the DB shard?
Sharding is a method of splitting and storing a single logical dataset in multiple databases.

Setting up the DB communication
Processing Service - that stores data in the database.
Query Service - that retrieves data from the database. 
Proxy Server - Several machines, services that talk to the database need to know how many machines exist and which one to pick to store and retrieve data. 
Cluster Proxy - Calls a particular shard for use reducing resource strain. Can retrieve data from the master or slave database.
Shard Proxy - Sits in front of the database shard to cache query results, monitor database instance health, publish metrics, and terminate queries that take too long to return data.
Configuration Service (Like Zookeeper) - notifies and maintains a health check connection to all shards.
Service Registry - Highly available service which can perform health checks to determine health of each registered instance.
Partition registers itself in Zookeeper, while every partitioner service instance queries Zookeeper for the list of partitions.


Read Replica - copy of the master database that is used for read purposes only. Data is written to this server from the master. Data is either synchronously or asynchronously replicated.
Sync -  Always on, continuous, uses a lot of bandwidth, and cost more
Async - Intermittent, uses minimal bandwidth, and cost a lot less


NoSQL Database Design
Understand the queries you will perform (large to small)
No DB Normalization is needed.
Store everything required together.
Columns are added instead of rows (Relational Database)

Can the DB shard?
Chunks is a method of splitting and storing a single logical dataset in multiple databases (nodes) in NoSQL

All nodes communicate with each other.
No configuration service needed.
Gossip protocol - Setup each node to talk based on standards so information about each node propagates throughout the cluster.
To reduce network load, we do not need each node to talk to every other shard.
Clients of our database no longer need to call a special component for routing requests. Clients may call any node in the cluster and node itself will decide where to forward this
Coordinator Node
Decides which node stores data for the requested action using calls.
Implements an algorithm to determine which node does what action.
Calls multiple nodes to replicate data.
Perform Quorum Writes - Defines a minimum number of nodes that have to agree on the response. 
Coordinator Node initiates parallel writes. Waiting for X amount of responses from replicas may be too slow, so we may consider the write to be successful as soon as only Y replication requests succeeded.
Perform Quorum Reads - Defines a minimum number of nodes that have to agree on the response. 
Coordinator Node initiates parallel reads. Waiting for X amount of responses from replicas may be too slow, so we may consider the read to be successful as soon as only Y replication requests succeeded.
NoSQL Database Types - 
Column
Document
Key-Value
Graph








Replication
Single Leader Replication
Each partition will have a leader and several followers.While a leader stays alive, all followers copy events from their leader. And if the leader dies, we choose a new leader from its followers. If a follower dies, gets stuck, or falls behind, the leader will remove it from the list of its followers.
Multi Leader Replication
Multi-leader Replication is mostly used to replicate between several data centers.
Leaderless replication
Synchronous replication
Write data to primary storage and the replica simultaneously. the primary copy and the replica should always remain synchronized.
Good - 
Nearly instantaneous fail-over from primary to secondary data storage to occur leading to little to no application downtime.
 Bad - 
Requires consistent bandwidth (money)
Asynchronous replication
Write data to primary storage first. Then copies the data to the replica after. the primary copy and the replica should always remain synchronized. 
Good
Asynchronous replication requires substantially less bandwidth than synchronous replication.
It is designed to work over long distances. Since the replication process does not have to occur in real time, asynchronous replication can tolerate some degradation in connectivity.
Bad
No instant failover.
Database content mismatch.




















Processing Service - Take in the event and increment/decrement several counters
Must scale together with increase in use.
Enable partitioning 
Must process events quickly.
Store in memory
Must not lose when any machine crashes / DB unavailable.
Enable Replication and checkpointing.
Reads events from partition one by one, counts events in memory, and flushes these counted values to the database periodically.













Events arrive and we put them into that storage in order, one by one.
Fixed order allows us to assign an offset for each event in the storage. This offset indicates the event position in the sequence.
Events are always consumed sequentially.
Every time an event is read from the storage, the current offset moves forwards

Partitioning - Separate temp storage segments used to house data before it moves to the next service.
Instead of putting all events into a single queue, let's have several queues. Each queue is independent from the others. Every queue physically lives on its own machine and stores a subset of all events.
Partitioning allows us to parallelize events processing. More events we get, more partitions we create
Consumer establishes and maintains TCP connection with the partition to fetch data.
Consumer reads event it deserializes it, converting the byte array into the actual object.
Consumer is usually a single threaded component. Meaning that we have a single thread that reads events.
Multi-threaded access implemented several threads read from the partition in parallel.
Checkpointing becomes more complicated and it is hard to preserve order of events.
If the same message was submitted to the partition several times we need a mechanism to avoid double counting.
Use a distributed cache that stores unique event identifiers.
Use an In Memory counter aggregator (hash table that accumulates data for some period of time.)
Periodically, we stop writing to the current hash table and create a new one.
New hash table keeps accumulating incoming data.
Old hash table is no longer counting any data and each counter from the old hash table is sent to the internal queue for further processing.
By sending data to the internal queue we decouple consumption and processing.
Database Writer - sends pre-aggregated values to the database
Database writer is either a single-threaded or a multi-threaded component.
Each thread takes a message from the internal queue and stores pre-aggregated data in the database
Single-threaded version makes checkpointing easier.
Multi-threaded version increases throughput
Dead Letter Queue:
Section for messages that are sent if they can’t be routed to their correct destination (read or write)
Protect ourselves from database performance, network, slowness, or availability issues.
Store undelivered messages on a local disk of the processing service machine.
The second concept is data enrichment.
Store data the way it is queried (All the data stays with the video)
Embedded Database 
DB that lives on the same processing server.
Same machine eliminates a need for remote calls.
Checkpointing - Temp storage used to house data before it moves to the next service.
After we processed several events and successfully stored them in the database, we wrote a checkpoint to some persistent storage. If the processing service machine fails, it will be replaced with another one and this new machine will resume processing where the failed machine left off.


These are important concepts in Stream Data Processing.




































Two Processing Service options: 
Process data as it is received incrementing
Accumulate data in memory and add accumulated value to the database counter.

Option two is better due to caching. Data is aggregated to memory.
Push - Monitoring Service sends events synchronously to the Processing Service.
Processing service updates in-memory counters, returns successful responses back to clients. Data is lost if anything crashes.
Pull - Processing Service pulls events from some temporary storage.
Better fault-tolerance support and easier to scale
Data is placed in temp storage, then removed when processed
Processing service machine pulls events and updates in-memory counters.
Data remains if anything crashes.



































State Management - Keep counters in memory for some period of time.
Either in in-memory store or internal queue. Every time we keep anything in memory we need to understand what to do when a machine fails and this in-memory state is lost.
Events stored for a short period of time - re-create the state from raw events from scratch and reprocess them.
Events stored for a long period of time - Periodically save the entire in-memory data to a durable storage. A new machine just re-loads this state into its memory when started.

Partitioner Service - Distribute data across partitions using a load balancer
Request goes through API Gateway, a component that represents a single-entry point into the content delivery system. API Gateway routes client requests to backend services.
Setup Partitioner service in DNS, specify domain name, for example partitionerservice.domain.com and associate it with IP address of the load balancer device. So, when clients hit domain name, requests are forwarded to the load balancer device.
Partitions is also a web service that gets messages and stores them on disk in the form of the append-only log file.
Partitioner service has to use some rule, partition strategy, that defines which partition gets what messages.

Blocking system creates one thread per connection.
A blocking system is one that must wait until the action can be completed. read() would be a good example - if no input is ready, it'll sit there and wait until some is (provided you haven't set it to non-blocking, of course, in which case it wouldn't be a blocking system call).
Client makes a request, the socket that handles that connection on the server side is blocked.
This happens within a single execution thread.
Thread that handles that connection is blocked as well.
Client2 sends a request at the same time, we need to create one more thread to process that request.
This happens within a single execution thread.
Thread that handles that connection is blocked as well.

Modern multi-core machines can handle hundreds of concurrent connections each.
Server starts to experience issues (limited bandwidth, increase in number of connections and threads)
Use a rate limiter to keep the system stable.

Blocking I/O system calls (a) do not return until the I/O is complete. 
Most I/O requests are considered Blocking requests, meaning that control does not return to the application until the I/O is complete. The delay from system calls such as read() and write() can be quite long. Using system calls that block is sometimes called Synchronous programming. In most cases, the wait is not really a problem because the program can not do anything else until the I/O is finished. However, in cases such as network programming with multiple clients or with graphical user interface programming, the program may wish to perform other activity as it continues to wait for more data or input from users.
Blocking Systems are easy to debug. In blocking systems we have a thread per request and we can easily track progress of the request by looking into the thread's stack. Exceptions pop up the stack and it is easy to catch and handle them. We can use thread local variables in blocking systems. All these familiar concepts either do not work at all or work differently in the non-blocking world.

Non-Blocking I/O system calls return immediately. The process is later notified when the I/O is complete.
Asynchronous programming techniques with Non-Blocking system calls. An asynchronous call returns immediately, without waiting for the I/O to complete. The completion of the I/O is later communicated to the application either through the setting of some variable in the application or through the triggering of a signal or call-back routine that is executed outside the linear control flow of the application.
When we can use a single thread on the server side to handle multiple concurrent connections. Server just queues the request and the actual I/O is then processed at some later point. Piling up requests in the queue are far less expensive than piling up threads. Non-blocking systems are more efficient and as a result has higher throughput.
Price of non-blocking systems is increased complexity of operations.

Thousands of video view events happening on Youtube every second.
To process all these requests, API Gateway cluster has to be big in size; thousands of machines.
If we then pass each individual event to the partitioner service, the partitioner service cluster must be large also.
This is not efficient.

Batching - Combine events together and send several of them in a single request to the partitioner service.
Instead of sending each event individually, we first put events into a buffer. We then wait up to several seconds before sending the buffer's content or until the batch fills up, whichever comes first.
There are many benefits of batching: it increases throughput, it helps to save on cost, request compression is more effective.
Drawbacks - 
It introduces some complexity both on the client and the server side. For example, think of a scenario when a partitioner service processes a batch request and several events from the batch fail, while others succeed. 
Should we resend the whole batch?
Should we resend the failed events?

Timeouts define how much time a client is willing to wait for a response from a server.
Connection Timeout
Connection timeout defines how much time a client is willing to wait for a connection to establish.
Usually this value is relatively small, tens of milliseconds because we only try to establish a connection, no heavy request processing is happening just
Request timeout.
Request timeout happens when request processing takes too much time, and a client is not willing to wait any longer. 
To choose a request timeout value we need to analyze latency percentiles. For example we measure latency of 1% of the slowest requests in the system. And set this value as a request timeout. It means that about 1% of requests in the system will timeout. And what should we do with these failed requests? Let's retry them.
Retry Storm Event- If all clients retry at the same time or do it aggressively and overload the server with too many requests.
Exponential Backoff Algorithm - Increases the waiting time between retries up to a maximum backoff time.We retry requests several times, but wait a bit longer with every retry attempt.
Jitter - Adds randomness to retry intervals to spread out the load. If we do not add jitter, the backoff algorithm will retry requests at the same time and jitter helps to separate retries.
Circuit Breaker - Pattern that stops a client from repeatedly trying to execute an operation that's likely to fail. We simply calculate how many requests have failed recently and if the error threshold is exceeded we stop calling a downstream service. Some time later, a limited number of requests from the client are allowed to pass through and invoke the operation. If these requests are successful, it's assumed that the fault that was previously causing the failure has been fixed. We allow all requests at this point and start counting failed requests from scratch.
Makes the system more difficult to test
Difficult to set up error thresholds and timers.















Load Balancer - 
LB must know about service machines, we need to explicitly tell the load balancer the IP address of each machine. Both software and hardware load balancers provide API to register and unregister servers.
Load balancers need to know who is healthy or dead. This way load balancers ensure that traffic is routed to healthy servers only.
Load balancer pings each server periodically for a health check. If the LB doesn't respond, it stops sending traffic to it. Traffic resumes once healthy.
High availability of load balancers, they utilize a concept of primary and secondary node
Primary load balancer accepts connections and serves requests.
Secondary load balancer monitors the primary.
If, for any reason, the primary load balancer is unable to accept connections, the secondary one takes over. Primary and secondary should live in different data centers, in case one data center goes down.
Load balancers may use several algorithms to distribute the load.
Round Robin Algorithm - distributes requests in order across the list of servers.
Least Connections Algorithm - sends requests to the server with the lowest number of active connections.
Least Response Time Algorithm - sends requests to the server with the fastest response time.
Hash-Based Algorithm -  distribute requests based on a key we define, such as the client IP address or the request URL.

TCP LB 
Load Balancers simply forward network packets without inspecting the content of the packets. Think of it as if we established a single end-to-end TCP connection between a client and a server. TCP load balancers are fast and handle millions of requests per second.
HTTP LB 
Terminate the connection. Load balancer gets an HTTP request from a client, establishes a connection to a server and sends a request to this server. HTTP load balancers can look inside a message and make a load‑balancing decision based on the content of the message.
Hardware LB
Hardware load balancers are powerful network device machines with many CPU cores, memory and they are optimized to handle very high throughput. Millions of requests per second.
Software LB
Software load balancer is only software that we install on hardware we choose. Many software load balancers are open source.

