## Apache Kafka

### Kafka configuration with 3 kafka node in a Kafka Cluster installation process:

- Step1: download kafka from website, installation command: 

  ```shell
    wget https://archive.apache.org/dist/kafka/2.8.2/kafka_2.13-2.8.2.tgz
  ```

  for unzip the kafka file :
  ```shell
  tar -xf kafka_2.13-2.8.2.tgz
  ```

- Step2: create a directory `Data` in the current directory. Here all the data,logs, topic data will be stored.
   
  ```shell
  mkdir Data
  cd Data
  mkdir broker-0 broker-1 broker-2 zookeeper
  ``` 
- Step3: lets configure the zookeeper.properties file.
  ```shell
  echo "dataDir=/home/ruhul/real_time_etl/data/zookeeper" >> config/zookeeper.properties

  echo "admin.enableServer=true" >> config/zookeeper.properties
  
  echo "maxClientCnxns=50" >> config/zookeeper.properties

  echo "admin.serverPort=9090" >> config/zookeeper.properties

  echo "server.1=localhost:2888:3888" >> config/zookeeper.properties

  ```

  or simply we can execute the following command 
  ```shell
  echo -e "dataDir=/home/ruhul/real_time_etl/data/zookeeper\nadmin.enableServer=true\nmaxClientCnxns=50\nadmin.serverPort=9090\nserver.1=localhost:2888:3888" >> config/zookeeper.properties

  ```
- Step4: Now lets start zookeeper server.

  ```shell
  bin/zookeeper-server-start.sh config/zookeeper.properties
  
  ```

  for checking if zookeeper server has started or not execute `jps`

  To check all the status or monitoring zookeeper we can use zookeeper shell.

  ```shell
  bin/zookeeper-shell.sh localhost:2181
  
  ```
  Here our Standalone Zookeeper Server configuration has been successfully completed.

  Now we will configure 3 broker and execute them in kafka cluster.

- Step5: creating 3 server.properties
  ```shell
   mv server.properties server-0.properties
   cp server-0.properties server-1.properties

   cp server-1.properties server-2.properties
  ```

- Step6: Configuring the first broker-0.
  
  config/server-0.properties
  
  ```shell
  broker.id=0
  listeners=PLAINTEXT://localhost:9092
  log.dirs=/home/ruhul/real_time_etl/data/broker-0
  num.partitions=3
  zookeeper.connect=localhost:2181
  
  ```
- Step7: Configuring the second broker-1 file

  config/server-1.properties

  ```shell
  broker.id=1
  listeners=PLAINTEXT://localhost:9093
  log.dirs=/home/ruhul/real_time_etl/data/broker-1
  num.partitions=3
  zookeeper.connect=localhost:2181

  ```

- Step8: Configuring the third broker-2 file
  config/server-2.properties
  ```shell
  broker.id=2
  listeners=PLAINTEXT://localhost:9094
  log.dirs=/home/ruhul/real_time_etl/data/broker-2
  num.partitions=3
  zookeeper.connect=localhost:2181
  ```

- Step9: Execute the following scripts for running kafka server
  
  Run these scripts in seperate terminal, -d option will run in detach mode
  ```shell
  bin/kafka-server-start.sh config/server-0.properties
  bin/kafka-server-start.sh config/server-1.properties
  bin/kafka-server-start.sh config/server-2.properties
  ```
  lets check the status of brokers using `bin/zookeeper-shell.sh localhost:2181` 
  ```shell
  ls /brokers/ids
  ```

- Step10: Lets check the topics list:
  
  ```shell 
  bin/kafka-topics.sh --list --zookeeper localhost:2181
  // or
  bin/kafka-topics.sh --bootstrap-server localhost:9092  --list

  ```

 To describe the details of a topic:
 ```shell
bin/kafka-topics.sh --zookeeper localhost:2181 --topic first-topic --describe

 ```
- Step11: create a producer
  
  ```shell
  bin/kafka-console-producer.sh --broker-list localhost:9092,localhost:9093,localhost:9094 --topic first-topic
  // not mandatory to provide all the brokers address.
  ```
- Step12: create a consumer and listen the topic 

  ```shell 
  bin/kafka-console-consumer.sh --bootstrap-server localhost:9092,localhost:9093 --topic first-topic --from-beginning

  ```

  creating a consumer group

  ```shell
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first-topic --from-beginning --group first-group

  ```