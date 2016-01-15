## Goals
Ellis Island intends to resolve the following issues with data engineering:

1.  Efficient and scaleable storage of large volumes of data
2.  Low latency read/write with a lambda architecture for real-time data pipelines
3.  Production-ready REST API access without additional implementation

With Hadoop, we solve **(1)** by nature of its design.  Horizontally scalable, its distributed nature means little concern with expanding storage and computational power, with constant and consistent performance that similarly scales.  In addressing **(2)** when creating data pipelines that are performant and production ready, we are leveraging a [lambda architecture](http://lambda-architecture.net/) design to create a speed layer in processing and writing data.  (See [design](#design) ).  Finally, to make Ellis Island production ready **(3)**, we provided REST APIs at both ends for write and consumption.  Users do not need to understand the underlying implementation behind Ellis Island to incorporate their needs into their own projects.

See the [components](https://github.com/maximusjesse/EllisIslandPublic/wiki/Ellis-Island-Components) page to read more about each tool in detail.

## Infrastructure and DataFlow
<p align="center"><img src=https://raw.githubusercontent.com/maximusjesse/EllisIslandPublic/master/images/VertaforeDataFlow.PNG></p>

## Design
<p align="center"><img src=http://i.imgur.com/tzAFJ6i.png height="600"></p>

### Datastore
In considering the need to store historical data for long-term versus real-time data for immediate low-latency consumption, the dual layer design appropriately writes data to two storage locations.  [Apache HBase](http://hbase.apache.org/) serves as the end-point of the speed-layer, where records will live for 30 days.  Consumers will be able to use a key-value store to retrieve data, with the ability to filter in the GET request.  HBase is most appropriate for low-latency, sub-second retrieval.  We plan to incorporate [Apache Phoenix](https://phoenix.apache.org/) to allow SQL-like queries on HBase.

All data will be written to [Apache Hive](https://hive.apache.org/) for long-term archiving.  Initial design puts storage at 1 year, although most likely this will be expanded to be a permanent store in the future.  Hive records are also accessible through the same REST API, with slightly slower performance.  Hive has more powerful SQL-like querying abilities for more in-depth consumption.  At this moment, users are only allowed to retrieve records, and will not be able to directly manipulate Hive databases.

In addition to these databases, the data will also be written in raw text form to the HDFS directory specified in your own Ferry configurations.

### Ellis Island Ferry
The Ellis Island [Ferry properties](https://github.com/maximusjesse/EllisIslandPublic/wiki/Creating-Your-Ferry) file is used to allow teams to define their pipelines to their requirements.  Each pipeline will be started with its own unique Ferry.  This allows Ellis Island to create specific Kafka topics to write to, as well a unique Storm topologies to write your data to its specific HBase and Hive databases.

### Kafka
[Apache Kafka](http://kafka.apache.org/) serves as the point for users to write their data directly to a specified topic using the REST API POST command.  Kafka is a faultless messaging service that will serve as the source for Storm processing.  We strongly advise you to read over the Kafka documentation if you'd like to learn more.

### Storm
For each data pipeline, we use [Apache Storm](https://storm.apache.org/) to create topologies that process and write data.  This is where the Hive and HBase write occurs.  Simultaneously, the raw data will be stored in HDFS for backup and storage.  