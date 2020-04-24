Install Confluent Kafka on Dataproc Cluster
=============================================

#### Install Confluent Kafka using below commands:
```bash
cd ~
wget -qO - https://packages.confluent.io/deb/5.2/archive.key | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/5.2 stable main"
sudo apt-get update && sudo apt-get install confluent-platform-2.12
```

### Give 777 permission to /var/lib/zookeeper 
```bash
sudo chmod -R 777 /var/lib/zookeeper
```
#### Start Kafka
Let’s start the Kafka Services:
```bash
sudo systemctl start confluent-zookeeper
sudo systemctl start confluent-kafka
```

#### You can check service status with this command:
```bash
sudo systemctl status confluent-zookeeper
sudo systemctl status confluent-kafka
```
