# ES Demo

## Overview

ElasticSearch POC to create indexes, insert data, using various analyzers running queries (searching, aggregations, sorting, pagination, filters, fuzzy queries). ElasticSearch-Demo.postman_collection.json file contains collection of postman REST calls that can be useful to practice the ES concepts mentioned before. These examples are based on  [ES Udemy course](https://www.udemy.com/course/elasticsearch-7-and-elastic-stack/). 
Datasets used can be found under `data` directory.

## Logstash

### Importing Apache log file data

`logstash/conf.d/logstash.conf` file contains configuration to insert data from Apache log file to ElasticSearch.

Run `docker-compose up` to start ES and logstash. Logstash will start inserting data from `/logstash/data/access_log` file once ES and logstash container start running.

### Importing MySQL data

Uncomment `mysql` container in `docker-compose.yml`. Run `docker-compose up` to start running the containers. Exec in `mysql` container once it starts running.

Run the following commands to create table and insert data to it:

1. ```mysql -u es-demo --local-infile=1 -D movielens -p```

1. ```CREATE TABLE movielens.movies(movieID INT PRIMARY KEY NOT NULL, title TEXT, releaseDate DATE) ENGINE=InnoDB DEFAULT CHARSET=utf8;```
   
1.  ```LOAD DATA LOCAL INFILE '/data/ml-100k/u.item' INTO TABLE movielens.movies FIELDS TERMINATED BY '|' (movieID, title, @var3) set releaseDate = STR_TO_DATE(@var3, '%d-%M-%Y');```\

`logstash/conf.d/mysql.conf` file contains configuration to insert data from MySQL table to ElasticSearch. Update `logstash` container to use this configuration file. Restart container to start ingesting MySQL table data to ES.

### Importing Apache Kafka Data

Uncomment `zookeeper` and `kafka` containers in `docker-compose.yml`. `kafka-logs` kafka topic will be created when `kafka` container starts running.

`logstash/conf.d/kafka.conf` file contains configuration to insert data from Kafka topic to ElasticSearch. Update `logstash` container to use this configuration file. Logstash will insert data to ES when data is sent to `kafka-logs` topic.

Run `docker-compose up` to start these containers.

Exec in `kafka` container to send data. Run the following to start sending data to `kafka-logs` topic:

```
$KAFKA_HOME/bin/kafka-console-producer.sh --broker-list=`broker-list.sh` --topic kafka-logs < /data/access_log
```