Getting started with KSQL
=============================

KSQL queries data in a Kafka cluster, you will need to bring up a Kafka cluster, including ZooKeeper and a Kafka broker using below command -
```bash
confluent start
``` 
After you have successfully started the Kafka cluster and started KSQL, 

#### Create topic "pageviews".
```bash
kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic pageviews
```
 
#### Create topic "users".
```bash
kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic users
```

#### Open KSQL prompt (Ignore error related to /etc unknown host):
```bash
ksql http://localhost:8089
```

#### Create a STREAM pageviews_original from the Kafka topic pageviews
Specifying the value_format of DELIMITED. Describe the new STREAM. 
Notice that KSQL created additional columns called ROWTIME, which 
corresponds to the Kafka message timestamp, and ROWKEY, which corresponds to the Kafka message key.
```bash
ksql> CREATE STREAM pageviews_original (viewtime bigint, userid varchar, pageid varchar) WITH (kafka_topic='pageviews', value_format='DELIMITED');
 
ksql> DESCRIBE pageviews_original;
```
 
#### Create a TABLE users_original from the Kafka topic users
Specifying the value_format of JSON. Describe the new TABLE.
```bash
ksql> CREATE TABLE users_original (registertime bigint, gender varchar, regionid varchar, userid varchar) WITH (kafka_topic='users', value_format='JSON', key='userid');

ksql> DESCRIBE users_original;
```

#### Show all STREAMS and TABLES.
```bash
ksql> SHOW STREAMS;
 
ksql> SHOW TABLES;
```