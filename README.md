# Kafka Terminology

Kafka Terms
Broker - the individual kafka binary running on a host, or the server running kafka. This is the main Kafka server/service.

Cluster - a collection of brokers that are coordinating and working together to service producers and consumers

Controller - a Kafka service process that keeps track of and controls partition assignment, broker health and status, publishes cluster health metrics, etc. The controller is elected from the available brokers.

Consumer - a program that reads messages from kafka.

Consumer Group - a collection of consumers that all work together to consume messages from Kafka, coordinating their work and recovering/rebalancing if the group size changes.

Consumer Lag - when a consumer is reading messages from a partition, the difference between where the offset the consumer is reading and the offset where the producer is writing is called Lag. Too much lag means that consumers aren't able to keep up with the incoming data and latency is being added to your processing.

ISR - An In-Sync Replica of a partition. To be In-Sync a message must stay within a fixed number of offsets from the Leader. If it falls behind its no longer an ISR and cannot be made Leader until it comes back in to sync.

Leader - one of the replicas of a partition which is elected (or assigned) the role of handling ingest for that partition. All writes happen to the leader, and data is replicated from the leader to replicas.

Leader election - Leaders are elected from brokers that have an In-Sync Replica (ISR) of the data

Message - a constrained bit of data - could be a JSON object, a log line, an image file, a database transaction

Offline Partition - a partition that is no longer available to be produced to or consumed from because no brokers have a copy of the partition and so cannot make it available. This should never happen in a cluster, and is an indication that something has gone wrong.

Offset - a constantly increasing number that is a unique identifier for a message.

Partition - a portion of a Topic. Topics are divided up into partitions as a method of scaling throughput, as well as allowing for strict message ordering with appropriate configuration.

Producer - a program that writes messages to Kafka.

Topic - a collection of messages that are generally related. Messages are produced to a topic, and consumed from a topic. Topics can be consumed by multiple consumers.

Replica - a copy of a partition held on another broker in the cluster. Automatically kept in sync with the leader.

Replication lag - replicas of a partition will replicate data from the leader, reading Offsets in order. The difference between the Replicas offset and the Leaders latest offset is Replication Lag. High replication lag will cause a replica to no longer be an In-Sync Replica, and usually indicates that a cluster or partition is overloaded.

Unclean Leader Election - When there is a leade election, but there are no In-Sync Replicas (ISRs) available. And unclean leader election means that data was lost.

Zookeeper - a distributed data storage engine that Kafka uses to store metadata about the service, as well as using the Zookeeper leader election protocol to makde decisions about which broker is the leader for which partitions.





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

