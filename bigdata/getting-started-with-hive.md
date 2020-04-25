Getting started with Hive on Google Dataproc
=============================

Files used in this exercise:

#### Data Files -
```bash
hdfs dfs  -ls /user/$USER/data/
```

#### Load data folder into HDFS 
```bash
hdfs dfs -put data/ratings.csv data/
```

#### Invoke hive shell
```bash
hive
```

#### Create an External Table named “ratings” and insert data into it.
```bash
CREATE EXTERNAL TABLE ratings (
 userid int,
 movieid int ,
 rating int ,
 tstamp string
)ROW FORMAT  DELIMITED
 FIELDS TERMINATED BY ','
LOCATION '/user/{user}/data/ratings' ;

LOAD DATA INPATH '/user/{user}/data/ratings.csv' INTO TABLE ratings;
```

#### Verify the table has been created.
```bash
show tables ;
```

#### Select data from the table
```bash
SELECT * from ratings limit 10;
```

#### Count movies id where rating is greater than 2
```bash
SELECT count(movieid) from ratings where rating > 2;
```

#### Create and load  managed table name ratings_managed
```bash
CREATE TABLE ratings_managed (
 userid int,
 movieid int ,
 rating int ,
 tstamp string
)ROW FORMAT  DELIMITED
 FIELDS TERMINATED BY ',' ;

INSERT OVERWRITE TABLE ratings_managed SELECT * FROM ratings;
```

#### Verify the table Schema and data.
```bash
describe formatted ratings_managed;

dfs -ls /user/hive/warehouse/ratings_managed ;
```

#### Drop the table check file in table location /user/hive/warehouse/ratings_managed
```bash
drop table ratings_managed;

dfs -ls /user/hive/warehouse/ratings_managed;
```

**Note:**  Metadata and Data are deleted from Metastore and  HDFS respectively.

**Note:** Did you notice that you just executed ls command from the hive shell itself !!
