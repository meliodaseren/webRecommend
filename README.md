# Dataservice Recommendation Web Demo


## Data Source: Web Flask


## Change MySQL Password (Optional)
```shell
# mysqladmin -u root password '1'
```


## Load training data to mysql

```shell
# mysql -uroot iii -p -e "LOAD DATA LOCAL INFILE '~/webRecommend/weblog.csv' INTO TABLE weblog FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '\"' LINES TERMINATED BY '\n' "
```


## Ambari Web UI: Change folder [data]  [user] Permission


## Setup Ambari MySQL JDBC

```shell
# ambari-server setup --jdbc-db mysql --jdbc-driver /opt/mysql-connector-java-5.1.44/mysql-connector-java-5.1.44-bin.jar
```


## Use sqoop to import MySQL data to HDFS Repository

```shell
# sqoop import --connect jdbc:mysql://localhost/iii --username root --password 1 --driver com.mysql.jdbc.Driver --query "SELECT uuid, item, rating FROM iii.weblog WHERE 1=1 AND \$CONDITIONS" -target-dir /data/weblog -m1
```


## Algorithm: Mahout ALS-WR

```shell
# mahout parallelALS --input /data/weblog/* --output /data/logout --tempDir /data/tmp --numFeatures 5 --numIterations 2 --lambda 0.065
```


## Mahout recommendfactorized

```shell
# mahout recommendfactorized --input /data/logout/userRatings/ --output /data/recommend/ --userFeatures /data/logout/U --itemFeatures /data/logout/M --numRecommendations 3 --maxRating 5 --numThreads 2
```


## Upload data to MySQL

```shell
# mysql -uroot iii -p -e "LOAD DATA LOCAL INFILE '/tmp/mahout_result.csv' INTO TABLE recommend FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '\"' LINES TERMINATED BY '\n' "
```

---

Copyright 2017 https://github.com/orozcohsu/webRecommend