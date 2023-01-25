# MSK Comands For Creating a Cluster and Streaming Data

Install Java:
sudo yum install java-1.8.0

Get Kafka:wget https://archive.apache.org/dist/kafka/2.2.1/kafka_2.12-2.2.1.tgz

Extract Kafka:
tar -xzf kafka_2.12-2.2.1.tgz

Get Cluster ARN:
aws kafka describe-cluster --cluster-arn "ClusterArn" --region 

Create Topic:
bin/kafka-topics.sh --create --zookeeper "ZookeeperConnectString" --replication-factor 2 --partitions 1 --topic AWSKafkaTutorialTopic

User the Trust store:
cp /usr/lib/jvm/JDKFolder/jre/lib/security/cacerts /tmp/kafka.client.truststore.jks

client.properties:
security.protocol=SSL
ssl.truststore.location=/tmp/kafka.client.truststore.jks

Get Brokers:
aws kafka get-bootstrap-brokers --cluster-arn ClusterArn --region

Producer:
./kafka-console-producer.sh --broker-list BootstrapBrokerStringTls --producer.config client.properties --topic AWSKafkaTutorialTopic

Consumer:
./kafka-console-consumer.sh --bootstrap-server BootstrapBrokerStringTls --consumer.config client.properties --topic AWSKafkaTutorialTopic --from-beginning



Basic KAFKA Commands

START ZOOKEEPER
bin/zookeeper-server-start.sh config/zookeeper.properties

START KAFKA BROKER
bin/kafka-server-start.sh config/server0.properties
bin/kafka-server-start.sh config/server1.properties
bin/kafka-server-start.sh config/server2.properties

GET INFORMATION FROM ZOOKEEPER ABOUT ACTIVE BROKER IDS
bin/zookeeper-shell.sh localhost:2181 ls /brokers/ids

GET INFORMATION FROM ZOOKEEPER ABOUT SPECIFIC BROKER BY ID
bin/zookeeper-shell.sh localhost:2181 get /brokers/ids/0

CREATE TOPIC
bin/kafka-topics.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--create \
--replication-factor 3 \
--partitions 7 \
--topic months

LIST TOPICS
bin/kafka-topics.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--list

TOPIC DETAILS
bin/kafka-topics.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--describe \
--topic months

START CONSOLE PRODUCER
bin/kafka-console-producer.sh \
--broker-list localhost:9092,localhost:9093,localhost:9094 \
--topic months

START CONSOLE CONSUMER
bin/kafka-console-consumer.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--topic months

START CONSOLE CONSUMER AND READ MESSAGES FROM BEGINNING
bin/kafka-console-consumer.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--topic months \
--from-beginning

START CONSOLE CONSUMER AND READ MESSAGES FROM SPECIFIC PARTITION
bin/kafka-console-consumer.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--topic months \
--partition 6 \
--from-beginning

START CONSOLE CONSUMER AND READ MESSAGES FROM SPECIFIC PARTITION AND SPECIFIC OFFSET
bin/kafka-console-consumer.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--topic months \
--partition 3 \
--offset 2

