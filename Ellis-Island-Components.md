# Hadoop (and related technologies) 411

This page serves to give an introduction to the relevent technologies and tools we used within Ellis Island.  Hopefully, this will answer some questions regarding design, implementation, scaleability, and reliability.

## Hardware

<p align="center"><img src=insertlinkhere.jpg></p>

## Hadoop Core
[Hadoop](https://hadoop.apache.org/) is one of the cornerstones to "big data" in the last decade.  Originated from Yahoo's implementation of a Google paper, Hadoop consists of three core components:

1.  HDFS (Hadoop Distributed File System)
2.  MapReduce
3.  YARN (Yet Another Resource Manager)

Over the years, its ease of use and reliable scalability has put it as one of the most popular implementation choices for data engineering (we currently use it to drive the storage and processing of data for Vertafore's Analytics offering).  There's a bit of a misnomer amongst many, thinking that "Big Data"=Hadoop and vice versa.  This is not fundamentally correct, and as data engineering evolves, so will the technology used to solve these problems.  It is mostly true to say that Hadoop is the infrastructural foundation upon which many projects today use in data focused software, as is the case with Vertafore Analytics.

One additional note, it's more accurate these days to see Hadoop these days as an "ecosystem" rather than the core components it was based on.  This is mainly because over the last decade, numerous projects have originated and grown from Hadoop into various complex components themselves, and together with Hadoop form the rich and evolving scene in data processing.

### HDFS
Hadoop's Distributed File System is the core data store within the Hadoop ecosystem.  By nature, it stores data in blocks across a distributed cluster, with a default replication factor of 3 to prevent data loss.  HDFS forms the file I/O component of most Hadoop related technologies.  Since the only major hardware concerns in a Hadoop distribution is RAM and physical memory, this allows for cheap horizontable scalability.  Since adding a node involves a linear expansion of RAM/processing and hard disk space, there is little noticeable performance decrease while storage increases in HDFS.  In this way, the computational power of Hadoop scales linearly with storage virtually indefinitely (Facebook, LinkedIn, and various other engineering groups have clusters that span into thousands of nodes).

### MapReduce
MapReduce formed the core programatic model in distributed computing inside the Hadoop environment.  You can read up more about [MapReduce](https://en.wikipedia.org/wiki/MapReduce) and its applications, but in a general sense, Hadoop utilized MapReduce to optimize processing within a parallel and distributed system.

There are caveats with MapReduce jobs.  Firstly, it is optimal when used to process large amounts of data.  When working with small operations, the overhead associated means that there will be little to no performance benefits when compared to a standard single-threaded process run on a single node.  However, as single-threaded processes are exponentially more inefficient to run as data scales, MapReduce allows for virtually constant performance as nodes can scale infinitely.

These days, there are alternative solutions to distributed computing, but MR still has a place as a core component of many Hadoop related processes and technologies.

###YARN
YARN became the resource manager of choice inside the Hadoop ecosystem after its integration.  With clusters growing indefinitely, there must be a system to efficiently utilize computing power amongst the queue of jobs.  This is where YARN comes in.  Also known as MR2 (succeeding MR1), YARN allocates system resources appropriately in accordance to priority, job complexity, resource availability, and other various factors.  With a large enough cluster, YARN allows 