# Kafka

## Setup Preresequise
Install [JAVA JDK](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-18-04#installing-specific-versions-of-openjdk)

check Java version
```bash
java -version
```

## Setup Steps
Update package for local system
```bash
sudo apt update
```

Download Kafka
```bash
cd /usr/local
wget https://downloads.apache.org/kafka/2.4.1/kafka_2.12-2.4.1.tgz
tar -xzf kafka_2.12-2.4.1.tgz
mv kafka_2.12-2.4.1 kafka
```
Start the ZooKeeper server
```bash
cd kafka
bin/zookeeper-server-start.sh config/zookeeper.properties
```

Start the Kafka server
```bash
bin/kafka-server-start.sh config/server.properties
```

## Test
Create a test topic
```bash
cd kafka
bin/kafka-topics.sh  \
		--zookeeper dev-server-manager-1:2181 \
		--create \
		--replication-factor 1 \
		--partitions 1 \
		--topic test
```

Send messages by open a new terminal, and goto kafka path
```bash
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic test
Hello world
```

Receive the message by open a new terminal, and goto kafka path
```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

## Reference
[Kafka for beginners](https://medium.com/@patelharshali136/apache-kafka-tutorial-kafka-for-beginners-a58140cef84f)

[Kaka-node and Node-rdkafka](https://www.tivix.com/blog/node-js-kafka-producers)

[Kafka Benchmark](https://kafkaesque.io/performance-comparison-between-apache-pulsar-and-kafka-latency/)
