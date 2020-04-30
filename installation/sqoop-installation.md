Sqoop Installation on Dataproc cluster
=================

### Prerequisite
 * HDFS home directory should be setup before Sqoop installation [Access HDFS in Terminal](../bigdata/access_hdfs_in_terminal.md)

### Download Sqoop

For this Sqoop Installation tutorial, we are using version 1.4.7, that is, sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
For other versions of Sqoop you can follow this [link](http://archive.apache.org/dist/sqoop/1.4.5/)

In the terminal do wget to download the sqoop package.

```bash
wget http://apachemirror.wuchna.com/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
```

Extract the Sqoop tarball and move it to the “/usr/lib/” directory.

```bash
tar -xvf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
sudo mv sqoop-1.4.7.bin__hadoop-2.6.0 sqoop
sudo mv sqoop /usr/lib/
``` 

### Configuring profile

Use *vi* command to append the following lines to *~/.profile* file.

```bash
#Sqoop
export SQOOP_HOME=/usr/lib/sqoop 
export PATH=$PATH:$SQOOP_HOME/bin
```

Now, save and close the above file by executing *“:wq!”* command.

Execute below command to reload enviroment variables.
```bash
source ~/.profile
```

### Configuring Sqoop

While, we need to edit the sqoop-env.sh file, that is placed in the $SQOOP_HOME/conf directory, in order to configure Sqoop with Hadoop. 
Now, using the following command redirect to Sqoop config directory and copy the template file. 

```bash
cd /usr/lib/sqoop/conf
mv sqoop-env-template.sh sqoop-env.sh
```
 
Also, open sqoop-env.sh and edit the following lines 

```bash
export HADOOP_COMMON_HOME=/usr/lib/hadoop
export HADOOP_MAPRED_HOME=/usr/lib/hadoop-mapreduce
```

Now, save the above file by executing *“:wq!”* command.

Copy hive-site.xml file into sqoop conf

```bash
sudo cp /usr/lib/hive/conf/hive-site.xml /usr/lib/sqoop/conf
```

### Configure MySQL-connector-java

You will find mysql-connector file in /usr/share/java/

You need to set softlink of MySQL-connector in $SQOOP_HOME/lib

```bash
sudo ln -s /usr/share/java/mysql-connector-java-5.1.42.jar /usr/lib/sqoop/lib
```

### Verifying Sqoop

For verifying the Sqoop version we use the following command.

```bash
cd /usr/lib/sqoop/
./bin/sqoop-version
```

### Reset your mysql or mariadb root password

Stopping the Database Server

To change the root password, you have to shut down the database server beforehand.
You can do that for MySQL with:

```bash
sudo systemctl stop mysql
```
 
And for MariaDB with:

```bash
sudo systemctl stop mariadb
``` 

After the database server is stopped, you’ll access it manually to reset the root password.
Restarting the Database Server Without Permission Checking
Start the database without loading the grant tables or enabling networking:

```bash
sudo mysqld_safe --skip-grant-tables &
```
 
Now you can connect to the database as the root user, which should not ask for a password.
```bash
mysql -u root
```
 
You’ll immediately see a database shell prompt instead.
Changing the Root Password
Let’s tell the database server to reload the grant tables by issuing the FLUSH PRIVILEGES command.
```bash
FLUSH PRIVILEGES;
```
 
Now we can actually change the root password.
For MySQL 5.7.6 and newer as well as MariaDB 10.1.20 and newer, use the following command.
```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';
```
 
Reload the grant tables after this.
```bash
FLUSH PRIVILEGES;
```

Restart the Database Server Normally
First, stop the instance of the database server that you started manually 
```bash
sudo kill `sudo cat /var/run/mysqld/mysqld.pid`
``` 

Then, restart the service using systemctl.
```bash
sudo systemctl restart mariadb
```
 
Now you can confirm that the new password has been applied correctly by running:
```bash
mysql -u root -p
```

### Create database “demo” in mysql
```bash
create database demo;
use demo;
create table test ( id INT(6));
```
 
### Insert Data Into MySQL
```bash
INSERT INTO test (id) VALUES (1);
INSERT INTO test (id) VALUES (2);
INSERT INTO test (id) VALUES (3);
INSERT INTO test (id) VALUES (4);
INSERT INTO test (id) VALUES (5);
INSERT INTO test (id) VALUES (6);
INSERT INTO test (id) VALUES (7);
```

### Load sqoop data into hdfs
```bash
cd /usr/lib/sqoop

./bin/sqoop import --connect "jdbc:mysql://localhost/demo" --username root --password password --table test --target-dir="mydata" 
``` 

Verify the Output in HDFS
```bash
hdfs dfs -ls 
hdfs dfs -ls mydata
hdfs dfs -cat mydata/part*
``` 

### Import mysql table into hive
```bash
./bin/sqoop import --connect jdbc:mysql://localhost:3306/demo --username root --password password \
--table test --target-dir test --hive-import \
--create-hive-table --hive-table \
default.test -m 1
``` 

*Note:* If you face this error

Add 
*permission javax.management.MBeanTrustPermission "register";*
in /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/security/java.policy inside grant{ }; 

 
Now, save the above file by executing this command “:wq!”.
Update hive.execution.engine to “mr”
```bash
sudo vi /usr/lib/sqoop/conf/hive-site.xml
```
```bash 
 <property>
    <name>hive.execution.engine</name>
    <value>mr</value>
  </property>
```
 
Now execute the following command 
```bash
./bin/sqoop import --connect jdbc:mysql://localhost:3306/demo --username root --password password --table test --target-dir test --hive-import --create-hive-table --hive-table default.test 
```
 
Then Verify the table in hive uisng below command
```bash
hive
show databases;
show tables;
select * from test;
```
