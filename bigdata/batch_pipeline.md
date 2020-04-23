Big Data Batch Pipeline Use Avro format
==========================

#### Import retail_db database in mysql

Connect to MySQL with user root 
```bash
mysql  -uroot -p{specify password here} 
```
First, log in to the database as username: root to create new databases.

This will bring you into the MySQL shell prompt. Next, create a new database called retail_db.

```bash
mysql> CREATE DATABASE retail_db;
```
Now exit the MySQL shell by pressing CTRL+D. On the normal command line, you can import the dump file with the following command:
```bash
mysql -uroot -p{specify password here} retail_db < retail_db .sql
```
Follow below command to view database list, changing the database and to list the tables.
```bash
mysql> show databases;
```

#### Sqoop Import orders table from MySql to HDFS in AVRO format
```bash
./bin/sqoop import -Dmapreduce.job.classloader=true --connect jdbc:mysql://localhost/retail_db \
--username root --password password --table orders \
--as-avrodatafile --outdir schema \
--target-dir data/orders 
```
#### Sqoop Import customers table from MySql to HDFS in AVRO format
```bash
./bin/sqoop import -Dmapreduce.job.classloader=true --connect jdbc:mysql://localhost/retail_db \
--username root --password password --table customers \
--as-avrodatafile --outdir schema \
--target-dir data/customers 
```
#### Open spark-shell and pass --packages com.databricks:spark-avro_2.11:4.0.0
```bash
spark-shell --packages com.databricks:spark-avro_2.11:4.0.0
```

#### Open spark shell and execute below command
```bash
import com.databricks.spark.avro._

val customers= spark.read.format("com.databricks.spark.avro").option("header","true").load("data/customers/part-m-00000.avro")
customers.show(10)

val orders= spark.read.format("com.databricks.spark.avro").option("header","true").load("data/orders/part-m-00000.avro")
orders.show(10)

val df = customers.join(orders, $"customer_id" === $"order_customer_id").drop("order_id")

val customer_order = df.where("order_status = 'PENDING_PAYMENT'").select($"order_customer_id",$"customer_fname",$"customer_lname",$"customer_city", $"customer_state",$"order_status").show()
```


|order_customer_id|customer_fname|customer_lname|customer_city|customer_state|   order_status|
|-----------------|--------------|--------------|-------------|--------------|---------------|
|            11599|       Richard|     Hernandez|  Brownsville|            TX|         CLOSED|
|            12111|           Ann|         Smith|       Caguas|            PR|       COMPLETE|
|             8827|          Mary|         Jones|   San Marcos|            CA|         CLOSED|
|            11318|        Robert|        Hudson|       Caguas|            PR|       COMPLETE|
|             7130|          Mary|         Smith|      Passaic|            NJ|       COMPLETE|
|             4530|       Melissa|        Wilcox|       Caguas|            PR|       COMPLETE|
|             2911|         Megan|         Smith|     Lawrence|            MA|     PROCESSING|
|             5657|          Mary|         Perez|       Caguas|            PR|PENDING_PAYMENT|
|             5648|       Melissa|         Smith|     Stafford|            VA|PENDING_PAYMENT|
|             1837|   Christopher|         Smith|  San Antonio|            TX|         CLOSED|
|             9149|          Mary|       Baldwin|       Caguas|            PR|PENDING_PAYMENT|
|             9842|     Katherine|         Smith|  Pico Rivera|            CA|     PROCESSING|
|             2568|          Jane|          Luna|      Fontana|            CA|       COMPLETE|
|             7276|       Tiffany|         Smith|       Caguas|            PR|PENDING_PAYMENT|
|             2667|          Mary|      Robinson|       Taylor|            MI|       COMPLETE|
|             1205|        Robert|         Smith|     Martinez|            CA|         CLOSED|
|             9488|     Stephanie|      Mitchell|       Caguas|            PR|PENDING_PAYMENT|
|             9198|          Mary|         Ellis|West New York|            NJ|     PROCESSING|
|             2711|       William|     Zimmerman|       Caguas|            PR|        PENDING|
|             4367|      Benjamin|        Duarte|     San Juan|            PR|PENDING_PAYMENT|

#### Use partitionBy("customer_city") 
```bash
customer_order.write.partitionBy("customer_city").format("parquet").saveAsTable("customer_order")
```

#### Verify partition table hive 
In hive shell

```bash
show partition customer_order
```
