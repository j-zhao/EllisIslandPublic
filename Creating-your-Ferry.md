# Overview
[Storm topologies](https://storm.apache.org/) for each [Ellis Island Ferry](https://github.com/maximusjesse/EllisIsland/tree/master/EllisIsland_Java/src/main/resources) is defined by the **ferry.properties** file in the related config sections.  In general, the Ellis Island storm topology will read from a specified Kafka topic, making the topology a Kafka consumer, then write it to three destinations:

1.  HDFS (**##HDFS Topology**)
2.  Hive (**##Hive Topology**)
3.  HBase (**##HBase Topology**)

### Table configs
To properly write to a Hive table, it's important that the table schema is defined prior to write.  After setting the **tableName** and the primary key **rowKey** (this is necessary for HBase), EllisIsland will create identical Hive/Hbase tables to write to.  Setting columns is done by setting the **columnsName** property, where the table columns are written as a list, i.e. `col1, col2, col3, ...`.  

### Hive Configs
This section really doesn't need to be touched, as it connects to our Hive server.  Probably should black-box this...

### HDFS Topology
This specifies which directory to store your data in HDFS (as raw text).  This is probably the most efficient and safe storage method for long-term archiving, at the cost of accessibility.

### Hive/HBase Topology
Specify the names of your Hive/HBase topologies and bolts.