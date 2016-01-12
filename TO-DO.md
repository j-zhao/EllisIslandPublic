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

##Done
- GET         /hottrans/:tableName/:transId/:cf/:col should be changed so that cf is no longer a parameter, as cf is hard coded to "eventcf"

- Consider changing hottrans/coldtrans GET method so that it always looks for hot first, then cold