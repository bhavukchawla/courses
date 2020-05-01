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
#### Start the confluent service
**Let’s start the confluent Services:**
```bash
confluent start
```

#### You can check service status with this command:
```bash
confluent status
```

#### You can check the logs with this command:
```bash
confluent logs {service name}
```

#### Note: If you are facing below error

Trying to access the KSQL CLI facing below error:

Remote server at http://localhost:8088 does not appear to be a valid KSQL

server. Please ensure that the URL provided is for an active KSQL server.

![alt text](https://i.ibb.co/K29zwyD/5-C0-AP9-MDCF0-Z.png)
 
#### Edit ksql-server.properties 
```bash
sudo vi /etc/ksql/ksql-server.properties
```

Change listener 8088 to 8089 

listeners=http://localhost:8089

Save the file and restart confluent service

```bash
confluent stop

confluent start
```
Open KSQL prompt (Ignore error related to /etc unknown host):
```bash
ksql http://localhost:8089
```
![alt text](https://lh3.googleusercontent.com/UtFo1d_q8VQsHeseWLw6pwy4BW9oFFVG6hMdVGm4MQM2g6MGLeGzyI3vaFGKCOB1Ku-j6ANmczwOZFj-V2uzNcjodpEzFEIeqxYvJdX-khTFodhBtnGT_aSRA2XCVm1DC2pDaNA)
