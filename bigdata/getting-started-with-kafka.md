Getting started with Confluent Kafka
============================

Open terminal:
#### Create a topic: 
```bash
kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic datacouch
```
#### Confirm the topic is created:
```bash
kafka-topics --list --zookeeper localhost:2181
```

#### Produce a message
```bash
kafka-console-producer --broker-list localhost:9092 --topic datacouch
```
Press enter. No feedback appears; the cursor sits blinking and waiting for your input.

Type a message and press enter:
	Hello World!

#### Consume a message
Open another terminal:
```bash
kafka-console-consumer --bootstrap-server localhost:9092 --topic datacouch --from-beginning
```

#### Remove the topic
```bash
kafka-topics --delete --zookeeper localhost:2181 --topic datacouch
```
