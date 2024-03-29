# outlier-detection-system
Outlier Detection System

Given the following types of JSON messages:
```{"publisher": "publisher-id", "time": "2015-11-03 15:03:30.352","readings": [ 1, 13, 192, 7, 8, 99, 1014, 4]}```
are pushed to kafka, the present system is proposing two services to find the outliers of median readings: a cosumer which will store the median of the readings in a Database and a rest service which retrives the last given number of records and mark the outliers.

***
- [x] Consumer
- [x] Web
- [x] Tests (one of each covering the whole flow (:happypath:) :sleepy:)
## Requirements

* Java 1.8+
* Maven
* Docker for Kafka, Zookeeper, MySQL

### Kafka

```console
docker run --net=host -d --name=zookeeper -e ZOOKEEPER_CLIENT_PORT=2181 confluentinc/cp-zookeeper
```

```console
docker run --net=host -d -p 9092:9092 --name=kafka -e KAFKA_ZOOKEEPER_CONNECT=localhost:2181 -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 -e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 confluentinc/cp-kafka
```

### Database

```console
docker run --name readings-db --net=host -p 3306:3306 -e MYSQL_ROOT_PASSWORD=m4st3r -e MYSQL_DATABASE=readings_db -e MYSQL_USER=readingsuser -e MYSQL_PASSWORD=readingsus3r -d mysql
```

## Build (Maven)

```console
cd outlier-detection-system
mvn clean package
```

## Run

* Start consumer:
```console
java -jar readings-consumer\target\readings-consumer-0.0.1-SNAPSHOT.jar 
```

* Start web:
```console
java -jar outliers-web\target\outliers-web-0.0.1-SNAPSHOT.jar
```

## Sample calls:

* Publish some readings
```console
$ docker run --net=host --rm confluentinc/cp-kafka bash -c "echo '{\"publisher\":\"pub1\",\"time\":\"2019-12-03 13:8:03.040\",\"readings\":[7,8,9]}' | kafka-console-producer --request-required-acks 1 --broker-list localhost:9092 --topic outliers"
```

* Get outliers marked with true for specified publisher (pub1)
```console
curl -XGET localhost:8080/publishers/pub1/outliers?limit=10
```
