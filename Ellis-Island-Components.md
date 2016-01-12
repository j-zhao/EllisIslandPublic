# Hadoop (and related technologies) 411

This page serves to give an introduction to the relevent technologies and tools we used within Ellis Island.  Hopefully, this will answer some questions regarding design, implementation, scaleability, and reliability.
<!-- 
## Hardware

<p align="center"><img src=insertlinkhere.jpg></p> -->

## Hadoop Core
[Hadoop](https://hadoop.apache.org/) is one of the cornerstones to "big data" in the last decade.  Originated from Yahoo's implementation of a Google paper, Hadoop consists of three core components:

1.  HDFS (Hadoop Distributed File System)
2.  MapReduce
3.  YARN (Yet Another Resource Manager)

Over the years, its ease of use and reliable scalability has put it as one of the most popular implementation choices for data engineering (we currently use it to drive the storage and processing of data for Vertafore's Analytics offering).  There's a bit of a misnomer amongst many, thinking that "Big Data"=Hadoop and vice versa.  This is not fundamentally correct, and as data engineering evolves, so will the technology used to solve these problems.  It is mostly true to say that Hadoop is the infrastructural foundation upon which many projects today use in data focused software, as is the case with Vertafore Analytics.

One additional note, it's more accurate these days to see Hadoop these days as an "ecosystem" rather than the core components it was based on.  This is mainly because over the last decade, numerous projects have originated and grown from Hadoop into various complex components themselves, and together with Hadoop form the rich and evolving scene in data processing.  Hadoop primarily is a Java-based platform, although many related modern tools support various languages and implementations.

### HDFS
Hadoop's Distributed File System is the core data store within the Hadoop ecosystem.  By nature, it stores data in blocks across a distributed cluster, with a default replication factor of 3 to prevent data loss.  HDFS forms the file I/O component of most Hadoop related technologies.  Since the only major hardware concerns in a Hadoop distribution is RAM and physical memory, this allows for cheap horizontable scalability.  Since adding a node involves a linear expansion of RAM/processing and hard disk space, there is little noticeable performance decrease while storage increases in HDFS.  In this way, the computational power of Hadoop scales linearly with storage virtually indefinitely (Facebook, LinkedIn, and various other engineering groups have clusters that span into thousands of nodes).

### MapReduce
MapReduce formed the core programatic model in distributed computing inside the Hadoop environment.  You can read up more about [MapReduce](https://en.wikipedia.org/wiki/MapReduce) and its applications, but in a general sense, Hadoop utilized MapReduce to optimize processing within a parallel and distributed system.

There are caveats with MapReduce jobs.  Firstly, it is optimal when used to process large amounts of data.  When working with small operations, the overhead associated means that there will be little to no performance benefits when compared to a standard single-threaded process run on a single node.  However, as single-threaded processes are exponentially more inefficient to run as data scales, MapReduce allows for virtually constant performance as nodes can scale infinitely.

These days, there are alternative solutions to distributed computing, but MR still has a place as a core component of many Hadoop related processes and technologies.

### YARN
YARN became the resource manager of choice inside the Hadoop ecosystem after its integration.  With clusters growing indefinitely, there must be a system to efficiently utilize computing power amongst the queue of jobs.  This is where YARN comes in.  Also known as MR2 (succeeding MR1), YARN allocates system resources appropriately in accordance to priority, job complexity, resource availability, and other various factors.  With a large enough cluster, YARN allows high efficiency and low downtime in executing job queues.

## Ellis Island Lambda Architecture
In order to support a real-time streaming data pipeline, there are various components that come into play to prevent data loss and ensure high throughput.

### Kafka
[Kafka](http://kafka.apache.org/) originated at LinkedIn as a queue implementation to process log-type data.  A "pull" rather than "push" system, Kafka's design allows numerous Kafka clients (or consumers) to pull data at their own accord from Kafka topics/queue which Kafka brokers (or producers) write to.  One of the biggest advantages to this is that consumers can pull data at various paces independently of each other.

Let's go over a hypothetical scenario.  Suppose an application writes stock data to a Kafka topic, with a configuration to preserve each record for 1 week.  We could create various consumers to do various things with the data.  For example, one consumer could be a feature to alert a user whenever a stock drops under a certain threshold.  Another system could be aggregating and reporting stock performances.  Additionally, we could have a consumer that simply writes the raw data to various sources, such as an RDBMS.  Lastly, a machine learning model can be run on this data concurrently in real-time to help predict future trends.  As each consumer has differently complexity and throughput, they'll be processing each record at different times.  Kafka ensures that all records will be available in their 1 week retension lifetime, which various systems can then reliably process.

In Ellis Island, Kafka provides the queue for which each Ellis Island user writes their data to be processed in real-time.  Each Kafka topic is associated with its unique implemented Ferry.

### Storm
[Storm](https://storm.apache.org/) excels as one of the fastest distributed real-time streaming frameworks in modern data-architecture.  In terms throughput and speed, Storm has reliably been faster to many similar projects, such as Spark Streaming, Samza, and Flume.

<p align="center"><img src=insertlinkhere.jpg></p>

Storm's computational model utilizes "topologies" to create each individual process for data.  Data sources are known as "spouts", which pass data throughout the topology as tuples into bolts.  Bolts then compute data and write it forward to other parts in the topology.

Ellis Island uses Storm primarily to process and write data quickly and efficiently from a Kafka Topic.  When we receive data, suppose a delimited string containing an XML, Storm parses this data to be written.  Then, a separate bolt takes over and writes it to three locations: HDFS (raw), Hive (tuple), and HBase (tuple).  Each topology is unique to its associated Ferry and Kafka topic in our system.  In the future, these topologies can easily be expanded.  For instance, we could write a new bolt to shred and parse XML elements into its own tuple, to be written into an RDBMS or separate Hive and HBase tables.  Or, we could create a new topology to cause an alert whenever a premium inside a carrier return rating falls within certain parameters.  When speed is key, Storm is extremely powerful.