# Overview
[Storm topologies](https://storm.apache.org/) for each Ellis Island Ferry is defined by the [**ferry.properties**](https://github.com/maximusjesse/EllisIslandPublic/blob/master/ferry.properties) file in the related config sections.  In general, the Ellis Island storm topology will read from a specified Kafka topic, making the topology a Kafka consumer, then write it to three destinations:

1.  HDFS
2.  Hive
3.  HBase

### Required Configuration Parameters
*ferryID*

Create a unique ID for your ferry.  You can view existing ferries by using the **GET** */topics* API method to see if your ID already exists.

*tableName*

It's highly recommended to just use your ferryID as the table name to preserve uniqueness.  This will be the name of your Hive/HBase tables for cold/hot quotes

*rowKey*

This is the column name of the key you wish to define your messages.

*columnsName*

These are the names of your table columns.  To define multiple columns, write them as comma separated values, i.e.: `columnsName=col1,col2,col3,col4`

### Optional Configurations
*fetchSize*

This is the upper limit of your message size that is fetched from a Kafka topic.  Setting this as 10 mb means that you expect all your messages to fall under this limit.  If you feel that your messages are not being retrieved, you can try increasing this value.

*fieldDelimiter*

This is the delimiter of the messages you send.  For instance, if you wish to send messages in the form `key/001col1/001col2`, then you should define the delimiter here.  NOTE: the default delimiter is *\001*, however, this is defined as *\\001* in the properties due to how Java handles these values.

*batchSize*

Depending on how many messages you are streaming concurrently, it's advisable to tweak this value.  For instance, if you are streaming only 100 messages a day, it doesn't make sense to set a large batch size, as your tables will not update frequently enough to be insightful.  However, if you are streaming millions of messages daily, you may want to up this value to 100+, so to reduce the load in the entire process.  It's advisable to consult with us first before you determine what is appropriate and optimal.

*forceFromStart*

If you wish to read all messages in your Kafka Topic, leave this value as *true*.  If you wish to only process messages from henceforth, then you can change it to false.