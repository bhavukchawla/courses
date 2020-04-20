Spark Jobs Running on YARN
==============================

When running Spark on YARN, each Spark executor runs as a YARN container. Where MapReduce schedules a container and fires up a JVM for each task, 
Spark hosts multiple tasks within the same container. This approach enables several orders of magnitude faster task startup time.

Spark supports two modes for running on YARN, *“yarn-cluster”* mode and **“yarn-client”** mode. Broadly, **yarn-cluster** mode makes sense for production 
jobs, while yarn-client mode makes sense for interactive and debugging uses where you want to see your application’s output immediately.

Understanding the difference requires an understanding of YARN’s Application Master concept. In YARN, each application instance has an 
Application Master process, which is the first container started for that application. The application is responsible for requesting 
resources from the ResourceManager, and, when allocated them, telling NodeManagers to start containers on its behalf. Application Masters 
obviate the need for an active client — the process starting the application can go away and coordination continues from a process managed 
by YARN running on the cluster.

In **yarn-cluster** mode, the driver runs in the Application Master. This means that the same process is responsible for both driving the 
application and requesting resources from YARN, and this process runs inside a YARN container. The client that starts the app doesn’t 
need to stick around for its entire lifetime.

**yarn-cluster mode**
![alt text](https://i.ibb.co/3kkXjVJ/1.png "**yarn-cluster mode**")

The yarn-cluster mode is not well suited to using Spark interactively, but the **yarn-client** mode is. Spark applications that require user input, 
like spark-shell and PySpark, need the Spark driver to run inside the client process that initiates the Spark application. In yarn-client mode, 
the Application Master is merely present to request executor containers from YARN. The client communicates with those containers to schedule 
work after they start:

**yarn-client mode**
![alt text](https://i.ibb.co/3kkXjVJ/2.png "**yarn-client mode**")

The difference between standalone mode and yarn deployment mode,

1. Resource optimization won't be efficient in standalone mode.
2. In standalone mode, driver program launch an executor in every node of a cluster irrespective of data locality.
3. standalone is good for use case, where only your spark application is being executed and the cluster do not need to allocate resources for other jobs in efficient manner.

**This table offers a concise list of differences between these modes:**
![alt text](https://i.ibb.co/MZQWf0M/3.png)

Reference: https://blog.cloudera.com/blog/2014/05/apache-spark-resource-management-and-yarn-app-models/

### Understanding the difference between YARN Client and Cluster Modes

#### Open terminal go to /usr/lib/spark/examples/jars directory.
```bash
cd /usr/hdp/<VERSION NUMBER>/spark2/examples/jars
```

#### Import sherlock.txt data into HDFS
```bash
hdfs dfs -mkdir data/
hdfs dfs -put ~/data/sherlock.txt data/ 
```

#### Run Spark Submit command using --deploy-mode client to execute wordcount in terminal.

**Note:** Version of Spark examples jar file may change, just do ls in **/usr/lib/spark/examples/jars**
```bash
spark-submit --class org.apache.spark.examples.JavaWordCount --master yarn --deploy-mode client spark-examples_2.11-2.3.2.3.1.4.0-315.jar data/sherlock.txt output1
```

#### Output
![alt text](https://i.ibb.co/4VTSD5t/4.png)

**Note:** If you face this warning
``WARN org.apache.spark.scheduler.cluster.YarnScheduler: Initial job has not accepted any resources; 
check your cluster UI to ensure that workers are registered and have sufficient resources``

Then you need to change the values of **yarn.scheduler.maximum-allocation-mb** to 13gb and **yarn.scheduler.minimum-allocation-mb** to 2048mb in yarn-site.xml
which is located inside **/usr/lib/hadoop-yarn/etc/hadoop/**

##### yarn.scheduler.minimum-allocation-mb
It is the minimum allocation for every container request at the ResourceManager, in MBs. In other words, the ResourceManager can allocate containers 
only in increments of this value. Thus, this provides guidance on how to split node resources into containers. Memory requests lower than this will 
throw a InvalidResourceRequestException.
```bash
<name>yarn.scheduler.minimum-allocation-mb</name>
<value>2048</value>
```

##### yarn.scheduler.maximum-allocation-mb
The maximum allocation for every container request at the ResourceManager, in MBs. Memory requests higher than this will throw a InvalidResourceRequestException.
```bash
<name>yarn.scheduler.maximum-allocation-mb</name>
<value>13000</value>
```

#### Restart YARN ResourceManager service
```bash
sudo /etc/init.d/hadoop-yarn-resourcemanager restart
```

#### List Application 
```bash
yarn application -list
```

To view Logs of the Application, execute below command:
```bash    
yarn logs -applicationId {your application id}
```

#### Kill an Application
Below command kills a running application - 
```bash
yarn application -kill {your application id}
```

#### Run Spark Submit command using --deploy-mode cluster to execute wordcount in terminal.

**Note:** Version of Spark examples jar file may change, just do ls in **/usr/lib/spark/examples/jars**
```bash
spark-submit --class org.apache.spark.examples.JavaWordCount --master yarn --deploy-mode cluster spark-examples_2.11-2.3.2.3.1.4.0-315.jar data/sherlock.txt output1
```

You can check the output in YARN ResourceManager UI inside ApplicationID --> Logs
