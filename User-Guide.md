Since each user will most likely need to write their data to a unique source, it's important that a [Ferry](https://github.com/maximusjesse/EllisIslandPublic/wiki/Architecture#ellis-island-ferry) is properly created and the services are running properly before you attempt to write any data.  You can begin implementing your own Ferry [here](https://github.com/maximusjesse/EllisIslandPublic/wiki/Creating-Your-Ferry).  From this point forward, we will refer to your Ellis Island data pipeline as "ferries".

# Creating your Ferry
Reference the *Creating Your Own Ferry* page [here](https://github.com/maximusjesse/EllisIslandPublic/wiki/Creating-Your-Ferry).

# RESTFUL  API
###API Reference

The current RESTFUL web service is running on *172.19.4.59*.  For instance, using a method might look something like this:

`curl "172.19.4.59:9000/kafka/ellisislanddemo"`

Note that the ports are different for different services.  This allows users to continue using some methods even if certain services are down or inoperational.

---

**GET** *172.19.4.59:9000/topics*
Lists all topics existing on the Kafka cluster

**GET** *172.19.4.59:9000/kafka/\<topic\>*
Lists details for a specific Kafka topic.

**GET** *172.19.4.59:9001/hotlist*
Lists available "hot-quote" tables (HBase)

**GET** *172.19.4.59:9001/hottrans/\<tableName>/\<key\>/\<column\>*
Returns a particular element from a "hot-quote" table with ID:*transactionID* in the column:*column* (HBase).  Most appropriate for short-term, low-latency requests.

**GET** *172.19.4.59:9002/coldtrans/\<tableName\>/\<keyColName\>/\<key\>/\<column\>*
Returns a particular element from a "cold-quote" table with a key from *keyColName*, a key value:*key* in the column:*column* (Hive).  Most appropriate for long-term historical data, has higher latency.  It's important to note that Hive allows records to have the same key and data, so it's possible to have non-unique historical data stored.

**POST** *172.19.4.59:9000/kafka/\<topic\>*
Posts raw text to the specified topic.  Currently, this method only take in text data.  It's important to note that the text must be formatted precisely to how the table was defined in the *ferry.properties* configurations.  A sample request might look like: 

`curl POST --data "key<delim>col1<delim>col2<delim>col3" 192.168.139.68/kafka/ellisislanddemo`

###Writing/Inserting New Data into Ellis Island Tables
Due to the table design of the hot and cold quote storage, there are some things to note regarding how new rows are written and appended.

####Cold Storage

For cold quote storage, the data is stored in Hive, which is non-relational.  That means, you can write multiple instances of a record with the same "key" and columns.  For instance, if there are two rows:

**Customers**

| SSID         | First Name  | Last Name   | Timestamp            | Address         |
| ------------ | ----------- | ----------- | -------------------- | --------------- |
| 555-55-5555  | John        | Smith       | 2015-01-01 12:00:00  | 221B Baker St   |
| 555-55-5555  | John        | Smith       | 2015-03-05 18:00:00  | P. Sherman 42 Wallaby Way, Sydney |

A request `curl 172.19.4.59:9002/coldtrans/Customers/555-55-5555/Address` will actually return both "221B Baker St" and "P. Sherman 42 Wallaby Way, Sydney".  This is useful if you would like to track all instances of historical records, but keep this in mind when you are querying.

####Hot Storage

This is very different for hot quotes, which is stored in HBase.  HBase tables require that all keys are unique.  So, suppose that my table currently looks like this:

**Customers**

| SSID         | First Name  | Last Name   | Timestamp            | Address         |
| ------------ | ----------- | ----------- | -------------------- | --------------- |
| 555-55-5555  | John        | Smith       | 2015-01-01 12:00:00  | 221B Baker St   |

An example `curl POST -data "555-55-5555\001John\001Smith\0012015-03-05 18:00:00 \001P. Sherman 42 Wallaby Way, Sydney" 172.19.4.59:9000/kafka/customersTopic/` post request will result in a table looking like: 

**Customers**

| SSID         | First Name  | Last Name   | Timestamp            | Address         |
| ------------ | ----------- | ----------- | -------------------- | --------------- |
| 555-55-5555  | John        | Smith       | 2015-03-05 18:00:00  | P. Sherman 42 Wallaby Way, Sydney |

In short, new hot quotes will overwrite your old records and only persist the last record written, whereas cold quotes are always preserved as is.