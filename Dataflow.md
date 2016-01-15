# Dataflow
This page will discuss the dataflow of a record being written through an Ellis Island Ferry.

<p align="center"><img src=https://raw.githubusercontent.com/maximusjesse/EllisIslandPublic/master/images/Dataflow.png></p>

### Step 1
Suppose our sample data is some kind of delimited String of the form **Key | XML**.  Using the proper [Ferry configurations](https://github.com/maximusjesse/EllisIslandPublic/wiki/Creating-Your-Ferry), this String will be able to be written as a tuple.

In Step 1, a user writes this record using the Ellis Island API (see [User Guide](https://github.com/maximusjesse/EllisIslandPublic/wiki/User-Guide) ), where the record is encoded to a Base64 binary text.

### Step 2
The Ellis Island REST API takes the Base64 binary text and passes it through Confluent's REST Proxy API, which decodes this and writes it to a unique Kafka Topic that corresponds with a user Ferry.

### Step 3
Kafka stores each record into a topic as a queue, where it waits to be pulled by a Kafka consumer.

### Step 4
Various Storm topologies retrieve each individual record from its unique associated Kafka topic/queue.  In this step, Storm parses and processes each record in real-time.

### Step 4a
After a Storm topology finishes processing a record, it will write it as a tuple into two destinations.  The first is Hive and HDFS, where records will be stored for long-term archiving.  Storm writes through the native Hive Java client for optimal performance into a unique table associated with each Ferry.

### Step 4b
Like 4a, Storm writes a tuple into a unique HBase table associated with its corresponding Ferry, where it sits for instant access and low-latency retrieval.  At the moment, HBase has a retention period, where records past a certain interval will be deleted based on its timestamp written.