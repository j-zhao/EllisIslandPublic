Since each user will most likely need to write their data to a unique source, it's important that a [Ferry](https://github.com/maximusjesse/EllisIsland/tree/master/EllisIsland_Java/src/main/resources) (see [Architecture](https://github.com/maximusjesse/EllisIsland/wiki/Architecture)) is properly created and the services are running properly before you attempt to write any data.  This will need to be done on a case-by-case basis with guidance from Enterprise Analytics to ensure we meet your specific use case.

#REST API
## Writing data
We've used Confluent to provide a Kafka REST Proxy to allow direct writes to a Kafka topic.  To see the list of available topics to write to, run:

`$ curl "http://localhost:8082/topics"`

To POST/GET,

`$ curl -X POST -H "Content-Type: application/vnd.kafka.v1+json" \
      --data '{"id": "my_instance", "format": "binary", "auto.offset.reset": "smallest"}' \
      http://localhost:8082/consumers/my_binary_consumer  {"instance_id":"my_instance","base_uri":"http://localhost:8082/consumers/my_binary_consumer/instances/my_instance"}`

`$ curl -X GET -H "Accept: application/vnd.kafka.binary.v1+json" \
      http://localhost:8082/consumers/my_binary_consumer/instances/my_instance/topics/test
  [{"key":null,"value":"S2Fma2E=","partition":0,"offset":0}]`

Further specific commands will be included here once the environment is fully established.

## Consuming Data
*To be included*