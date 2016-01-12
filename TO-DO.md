#Ellis Island Feature Changes

##Major Changes
- Write webservice output to log
- Ellis Island API Errors Messages
- Storm Topologies Logging/Exception handling
- EllisIsland web ui to kick off ferries, maybe to monitor ferries as well
- Replace/Remove Confluent
- Impala?
- Smack/Mesos
- Bulk Import Process?

##Architecture/Performance
- Independent Kafka cluster?
- Test performance on Skytap/Varys
- Order Hive table to minimize query time
- User tokens, HDFS security
- Hive/Hbase permissions

- Setting up environments to support various environments

##Minor features
- Create Kafka topic if not exist
- Return entire tuple
- Added delimiter for coldtrans if multiple results
- Hive/HBase by attribute/column
- Hive/HBase tables should be created through Ferry if not exists or warns user
- List all rows/keys (Hive describe)
- Hive/Hbase table/column names case-sensitivity
- API methods consistent naming/clarity
- Delete/Create your own kafka_topics
- Hive/HBase table additional data types
- Kafka retention period

##Security
	- Kafka (http://kafka.apache.org/documentation.html#security) (SSL has performance impacts)
		- Encryption of data between brokers/clients, brokers, or brokers+external tools (Play Framework+SSL)
		- SSL or Kerberos of authentication from brokers and clients, or brokers and external tools
		-Consider others, but these seem to be the critical ones
	- Storm (https://github.com/apache/storm/blob/master/SECURITY.md)
		- Maybe not much to consider here other than SSL to the Kafka broker
	- HDFS (https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/TransparentEncryption.html)
		- Really don't want to think about this part, as I don't think 2.2 has many great tools besides native HDFS built-in encryption.  Would require 2.3 upgrade to be optimal.
		- https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/TransparentEncryption.html
	- Play Framework Get
		- SSL might be good enough
		- PII?
	- HBase (http://hbase.apache.org/book.html#security)
		- Rest vs HBase API Client
		- SASL
		- Kerberos
		- Thrift
		- Performance penalty
		- Probably not necessary, maybe only need to look at HBase data at rest encryption

##Done
- GET         /hottrans/:tableName/:transId/:cf/:col should be changed so that cf is no longer a parameter, as cf is hard coded to "eventcf"

- Consider changing hottrans/coldtrans GET method so that it always looks for hot first, then cold 