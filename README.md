# DocumentStreamingPipeline

#### Create an API 
using fast api , created one that takes post requests on path /invoiceitem
test it using postman. Import the json in this folder. 
note: if you are posting to local host, you have to get postman desktop agent. 

### Test produce kafka string

### Create a container with Kafka + zookeeper 
Use the docker-compose-kafka.yaml (contains kafka and zookeeper services)

start the container 
docker-compose -f docker-compose-kafka.yaml up

go to path /opt/bitnami/kafka/bin 

try the kafka commands:

- list topics 
./kafka-topics.sh --list --bootstrap-server localhost:9092

- Create Topic
./kafka-topics.sh --create --topic ingestion-topic --partitions 1 --replication-factor 1 --bootstrap-server localhost:9092
./kafka-topics.sh --create --topic spark-output --partitions 1 --replication-factor 1 --bootstrap-server localhost:9092

- Local consumer
./kafka-console-consumer.sh --topic ingestion-topic --bootstrap-server localhost:9092
./kafka-console-consumer.sh --topic spark-output --bootstrap-server localhost:9092

PROBLEM AT THIS STEP:
1. have to create the topic with --partitions and --replication-factor options
2. needed to specify the kafka version when creating kafka producer in the api app, otherwise it throws an error 'no broker found'
3. needed to expose 9093 to access externally , bootstrap server for producer is on port 9093. 

### Build the API container and deploy 
docker build -t api-ingest .

### Test the API container with the Kafka container 

Note: how to run the two containers on the same network so they can talk to each other

1. start the kafka container, find the networks name : in my case it's documentstreamingpipeline_default
2. command to start the api container: docker run --rm --network <networkname> --my-api-ingest -p 80:80 api-ingest;

3. test - go into the kafka container, start a consumer, send something from postman


### Use Spark to receive the evetns and write back to another kafka topic

1. Start the kafka-spark container
2. create the 'ingestion-topic' and 'spark-output' topic 







