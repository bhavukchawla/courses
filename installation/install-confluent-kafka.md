Install Confluent Kafka on Dataproc Cluster
=============================================

#### Install Confluent Kafka using below commands:
```bash
wget -qO - https://packages.confluent.io/deb/5.2/archive.key | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/5.2 stable main"
sudo apt-get update && sudo apt-get install confluent-platform-2.12
```

#### Configure Kafka
Let’s configure Kafka for a minimum install with 1 Kafka Broker, 1 Topic.

Navigate to your Kafka installation

Kafka Broker

Edit the file server.properties
```bash
sudo vi /etc/kafka/server.properties
```
Uncomment the following lines:
```bash
metric.reporters=io.confluent.metrics.reporter.ConfluentMetricsReporter
confluent.metrics.reporter.bootstrap.servers=localhost:9092
confluent.metrics.reporter.topic.replicas=1
```

Save with **:wq!** 

#### Control Center
Edit the file control-center-production.properties
```bash
sudo vi /etc/confluent-control-center/control-center.properties
```
Uncomment the following lines and edit to your server name or localhost:
```bash
bootstrap.servers=localhost:9092
zookeeper.connect=localhost:2181
```
Add the following lines at the end of the file:
```bash
confluent.controlcenter.internal.topics.partitions=1
confluent.controlcenter.internal.topics.replication=1
confluent.controlcenter.command.topic.replication=1
confluent.monitoring.interceptor.topic.partitions=1
confluent.monitoring.interceptor.topic.replication=1
```

#### Connector
```bash
sudo vi /etc/kafka/connect-distributed.properties
```
Add the following lines at the end of the file:
```bash
consumer.interceptor.classes=io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor
producer.interceptor.classes=io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor
```

#### Start Kafka
Let’s start the Kafka Services:
```bash
sudo systemctl start confluent-zookeeper
sudo systemctl start confluent-kafka
sudo systemctl start confluent-schema-registry
sudo systemctl start confluent-kafka-connect
sudo systemctl start confluent-kafka-rest
sudo systemctl start confluent-ksql
sudo systemctl start confluent-control-center
```

#### You can check service status with this command:
```bash
systemctl status confluent*
```
**Note**: If you face error while starting confluent-zookeeper like 
java.io.IOException: Unable to create data directory /var/lib/zookeeper
```bash
sudo chmod -R 777 /var/lib/zookeeper
sudo systemctl restart confluent*
```
