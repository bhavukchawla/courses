Write to a Stream and Table using KSQL
=================================

You already create a Stream **pageviews_original** and table **users_original** in [getting started with ksql](getting-started-with-ksql.md)
#### Produce topic data
Produce data to the Kafka topics pageviews and users.

Produce Kafka data to the pageviews topic using the data generator. The following example continuously generates data with a value in DELIMITED format. 
Open a terminal and don’t close this.
```bash
java -jar ksql-examples-4.1.0-standalone.jar quickstart=pageviews format=delimited topic=pageviews maxInterval=10000
```

#### Open KSQL prompt in another terminal (Ignore error related to /etc unknown host):
```bash
ksql http://localhost:8089
```
 
#### Use SELECT to create a query that returns data from a STREAM. 

To stop viewing the data, press <ctrl-c>. You may optionally include the LIMIT keyword to limit the number of rows returned in the query result. 
```bash
ksql> SELECT pageid FROM pageviews_original LIMIT 3;
```

#### Produce Kafka data to the users topic using the data generator. 
Open another terminal in parallel.
The following example continuously generates data with a value in JSON format. Open it in another terminal and don’t close this.
```bash
java -jar ksql-examples-4.1.0-standalone.jar quickstart=users format=json topic=users maxInterval=10000
```
#### Create a persistent query by using the CREATE STREAM keywords to precede the SELECT statement. 

Unlike the non-persistent query above, results from this query are written to a Kafka topic pageviews_female. 
The query below enriches the pageviews STREAM by doing a LEFT JOIN with the users_original TABLE on the user ID, where a condition is met.
```bash
ksql> CREATE STREAM pageviews_female AS SELECT users_original.userid AS userid, pageid, regionid, gender FROM pageviews_original LEFT JOIN users_original ON pageviews_original.userid = users_original.userid WHERE gender = 'FEMALE';
 
ksql> DESCRIBE pageviews_female;
```
 
Use SELECT to view query results as they come in. To stop viewing the query results, press <ctrl-c>. 
This stops printing to the console but it does not terminate the actual query. The query continues to run in the underlying KSQL application.
```bash
ksql> SELECT * FROM pageviews_female;
```
 
Create a new persistent query where another condition is met, using LIKE. Results from this query are written to a Kafka topic called pageviews_enriched_r8_r9.
```bash
ksql> CREATE STREAM pageviews_female_like_89 WITH (kafka_topic='pageviews_enriched_r8_r9', value_format='DELIMITED') AS SELECT * FROM pageviews_female WHERE regionid LIKE '%_8' OR regionid LIKE '%_9';
```

Create a new persistent query that counts the pageviews for each region and gender combination in a tumbling window of 30 seconds when the count is greater than 1. Results from this query are written to a Kafka topic called pageviews_regions.
```bash
ksql> CREATE TABLE pageviews_regions AS SELECT gender, regionid , COUNT(*) AS numusers FROM pageviews_female WINDOW TUMBLING (size 30 second) GROUP BY gender, regionid HAVING COUNT(*) > 1;
 
ksql> DESCRIBE pageviews_regions;
```
 
#### Use SELECT to view results from the above query.
```bash
ksql> SELECT regionid, numusers FROM pageviews_regions LIMIT 5;
```
 
#### Show all persistent queries.
```bash
ksql> SHOW QUERIES;
```
