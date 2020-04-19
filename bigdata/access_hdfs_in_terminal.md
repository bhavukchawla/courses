Access HDFS uisng Terminal
=================

|	Data		| data/movies.txt, data/ratings.txt |
| ------------- |:---------------:|

In this exercise, we will simply explore the HDFS file system using terminal.

#### Uploading to HDFS
Start by creating a new top-level directory for exercises. You will use this directory throughout the rest of the course.
```bash
hdfs dfs -mkdir data
```
Change directories to the local filesystem directory containing the sample data we will be using in the course.
```bash
cd ~/data
ls
```	
#### Insert this file into HDFS:
```bash
hdfs dfs -put ratings.txt data/
```

This copies the local ratings.txt file into a remote HDFS directory data/ . Notice we do not “cd” into HDFS.

#### List the contents of the new HDFS directory now:
```bash
hdfs dfs -ls data/
```
You should see the ratings.txt file in the local directory.

*NOTE:* In HDFS, relative (non-absolute) paths are considered 
relative to your home directory. There is no concept of a “current” or 
“working” directory as there is in Linux and similar file systems.

#### Practice uploading a directory, confirm the upload, and then remove it, as it is not actually needed for the exercises.
```bash
hdfs dfs -put movies.txt/ data/
hdfs dfs -ls data/
hdfs dfs -rm -r data/movies.txt
```

#### Viewing HDFS Files
Now view some of the data you just copied into HDFS.

1. Enter:
```bash
hdfs dfs -cat data/ratings.txt | head -n 20
```

This prints the first 20 lines of the article to your terminal.

2. To download a file to work with on the local filesystem use the

hdfs dfs -get command. This command takes two arguments: an HDFS path and a local path. It copies the HDFS contents into the local filesystem:
```bash
hdfs dfs -get data/ratings.txt ~/
less ~/ratings.txt
```

Enter the letter [q] to quit the less command after reviewing the downloaded
file.

There are several other operations available with the hdfs dfs command to perform 
most common filesystem manipulations such as mv, cp, and mkdir. In the terminal window, enter:

```bash
hdfs dfs
```

You see a help message describing all the file system commands provided by
HDFS. Try playing around with a few of these commands if you like.
