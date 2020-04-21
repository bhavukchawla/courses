Nifi Installation
==========================

Installation of Apache Nifi:

#### Open terminal.

#### Use wget command to downloaded Apache Nifi.
```bash
wget https://downloads.apache.org/nifi/1.11.4/nifi-1.11.4-bin.tar.gz 
```
Apache Nifi-1.11.4 tar file will be downloaded into your system.

### Implementation of Apache Nifi Server:

#### Extract the Nifi tarball and move it to the “/usr/lib/” directory.
```bash
tar -xvf nifi-1.11.4-bin.tar.gz 
sudo mv nifi-1.11.4-bin.tar.gz nifi
sudo mv nifi /usr/lib/
``` 

#### Change the directory to /usr/lib/nifi/
```bash
cd /usr/lib/nifi
```

#### Edit **nifi.properties**
Set the nifi.web.http.port to 9095 property in nifi.properties file present in conf directory. 
( Optional step, because sometimes the default 8080 port is taken for another process).
```bash
cd /usr/lib/nifi/conf
```
You can open this configuration file by using command **vi nifi.properties**.

![alt text](https://i.ibb.co/r3P8t12/5.png)

#### Verifying Nifi

For verifying the Nifi use the following command.
```bash
cd /usr/lib/nifi/
./bin/nifi.sh --help
```

#### Start Apache Nifi server
```bash
cd /usr/lib/nifi/
./bin/nifi.sh start 
```
You can see we can start, stop  apache nifi server also we can check the status.

You can check the logs created by starting the Apache nifi server by executing the command
```bash
tail -f ../logs/nifi-app.log (Optional).
```
![alt text](https://i.ibb.co/Zzfkjcw/6.png)

You have successfully started the Apache-Nifi server in the background. But to use Apache-Nifi UI, 
You have to set the firewall rules for the port 9095(nifi.web.http.port).

Now go to the browser, paste the external IP of the master node and add the 9095 port to it 
separated with ":" and add "/nifi" to it  
i.e. **<external_ip_of_masternode>:9095/nifi**.