# Kafka Terminology

![image](https://user-images.githubusercontent.com/36766101/214737462-355b7642-c155-42e5-bd34-da722cea5a59.png)


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


Now you can list all the available topics by running the following command:

kafka-topics \
  --bootstrap-server localhost:9092 \
  --list
  
 
Alternatively, you can also use your Apache Zookeeper endpoint. This can be considered legacy as Apache Kafka is deprecating the use of Zookeeper as new versions are being released.

kafka-topics \
  --zookeeper localhost:2181 \
  --list
  
  


# MSK Comands For Creating a Cluster and Streaming Data

Install Java:
sudo yum install java-1.8.0

Get Kafka:wget https://archive.apache.org/dist/kafka/2.2.1/kafka_2.12-2.2.1.tgz

Extract Kafka:
tar -xzf kafka_2.12-2.2.1.tgz

Get Cluster ARN:

CLUSTER_ARN=$(aws kafka list-clusters --query "ClusterInfoList[0].ClusterArn" --output text)

aws kafka describe-cluster --cluster-arn $CLUSTER_ARN --region 

Create Topic:

export MYZK=$(aws kafka describe-cluster --cluster-arn $CLUSTER_ARN --output json | jq ".ClusterInfo.ZookeeperConnectString" | tr -d \")


bin/kafka-topics.sh --create --zookeeper "ZookeeperConnectString" --replication-factor 2 --partitions 1 --topic AWSKafkaTutorialTopic


bin/kafka-topics.sh --zookeeper $MYZK --list

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

./kafka-topics.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--create \
--replication-factor 3 \
--partitions 7 \
--topic months

LIST TOPICS

./kafka-topics.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094 \
--list


./kafka-topics.sh --zookeeper $MYZK --list

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


# Create MSK 

Step 0 Prepare network configuration 

such as vpc, subnet, security group information

Step 1 Create Customer Configuration


aws kafka create-configuration --name "WorkshopMSKConfig" --description "xxxxxxxx" --kafka-versions "2.3.1" "2.2.1" --server-properties fileb://cluster_config.txt

aws kafka describe-configuration --arn $CLUSTER_ARN

Step 2 Create Cluster definitation file 

aws kafka create-cluster --cli-input-json file://clusterinfo.json 

aws kafka describe-cluster --cluster-arn arn:aws:kafka:us-east-1:xyz:cluster/MSKWorkshop/20a94343-552f-4298-9076-99673162e023-6 | grep -i state



# MSK Connect 
m![image](https://user-images.githubusercontent.com/36766101/221322858-713f2cf2-b3e7-4fc6-95e5-72fbb6998b5c.png)
![image](https://user-images.githubusercontent.com/36766101/221323322-699fbb09-c103-4bfd-b6bb-7187b3eb2786.png)
![image](https://user-images.githubusercontent.com/36766101/221323401-1aa9da05-fdfd-41ac-91db-84ac9b858a01.png)



![image](https://user-images.githubusercontent.com/36766101/214731613-d41845c0-d9e7-4ca2-b534-420d038e4a7d.png)

![image](https://user-images.githubusercontent.com/36766101/214731734-91d65dd1-94c1-40ae-9ea5-fd677b43f931.png)


MSK Connect is a feature of Amazon MSK that makes it easy for developers to stream data to and from their Apache Kafka clusters. MSK Connect uses Kafka Connect 2.7.1, an open-source framework for connecting Apache Kafka clusters with external systems such as databases, search indexes, and file systems


Kafka Connect provides a framework for moving data between Kafka and other systems. Moreover, a variety of useful connectors already exist to make this process even easier for common use cases. For example,We can use Kafka Connect to ingest data from an external database into Kafka. We will use the Java Database Connectivity (JDBC) Connector to automatically load data from a table in a PostgreSQL database into a Kafka topic.
![image](https://user-images.githubusercontent.com/36766101/221330654-d41f023d-2404-40d0-ba01-c41a656ffc8d.png)

![image](https://user-images.githubusercontent.com/36766101/221330791-4e45d53f-76a4-4825-96e8-99943c0b1d67.png)
![image](https://user-images.githubusercontent.com/36766101/221331000-5155abca-8aaf-43f1-943f-67f38ee57109.png)


# Schema Registry
![image](https://user-images.githubusercontent.com/36766101/221324576-2a152780-0727-4142-9563-7acda4ac165c.png)


# Get cluster broker list
aws kafka  get-bootstrap-brokers --cluster-arn arn:aws:kafka:us-east-1:xyz:cluster/MSKCluster/0546f493-019f-475a-9903-272f0371ce19-6 --output text

export MYBROKERS=$(aws kafka  get-bootstrap-brokers --cluster-arn arn:aws:kafka:us-east-1:xyz:cluster/MSKCluster/0546f493-019f-475a-9903-272f0371ce19-6 --output text)

bin/kafka-topics.sh --bootstrap-server $MYBROKERS --list


# Get your Zookeeper connection string

aws kafka describe-cluster --cluster-arn $CLUSTER_ARN --output json | jq ".ClusterInfo.ZookeeperConnectString"

export MYZK=$(aws kafka describe-cluster --cluster-arn $CLUSTER_ARN --output json | jq ".ClusterInfo.ZookeeperConnectString" | tr -d \")

bin/kafka-topcs.sh --zookeeper $MYZK --list

