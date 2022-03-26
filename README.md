# KafkaProducerExample
Simple KafkaProducer Example from the Confluent site

## Project
This is a maven project

├── conf  
│   ├── admin.properties.example  
│   └── producer.properties  
├── pom.xml  
├── README.md  
├── src  
│   └── main  
│       ├── java  
│       │   └── com  
│       │       └── kafkatools  
│       │           └── producer  
│       └── resources  
│           ├── avro  
│           │   └── com  
│           │       └── kafkatools  
│           │           └── example  
│           │               └── test.avsc  
│           └── txt  
│               └── com  
│                   └── kafkatools  
│                       └── example  
│                           └── input.txt  


## Dependencies
1. An AWS MSK cluster with IAM auth enabled  
2. An EC2 instance on the same VPC  
3. A policy defined allowing access to the AWS MSK and Glue resources in the account being used  
4. A role assigned to the EC2 instance and with the above policy attached  
5. Java Azul Zulu version 11 installed  
    ```$ sudo yum install zulu11```
6. Configure java to point to zulu11  
    ```$ alternatives --config java```  
7. Dowload the Kafka distribution  
        ```$ wget https://archive.apache.org/dist/kafka/2.8.1/kafka_2.13-2.8.1.tgz```  
        ```$ cd /opt && sudo tar xvzf ~/kafka_2.13-2.8.1.tgz```  
8. Stick the following jars into libs/ (see https://github.com/aws/aws-msk-iam-auth/ and https://github.com/awslabs/aws-glue-schema-registry/ for sources)  
        aws-msk-iam-auth-1.1.3-all.jar  
        schema-registry-common-1.1.9.jar  
        schema-registry-kafkaconnect-converter-1.1.9.jar  
        schema-registry-serde-1.1.9.jar  
        ```$ cd -``` #get back to the previous dir  

## Simple Example

1. run this producer from an EC2 instance with an MSK IAM access policy attached  
2. compile with Maven 2 from project root  
    ```$ mvn clean dependency:copy-dependencies install```  
3. configure the dependencies in ```conf/producer.properties``` (don't skip this  !)  
4. add a topic we can produce to in our example (check conf/admin.properties for example connection properties)
    ```cd /opt/kafka_2.13-2.8.1 && ./bin/kafka-topics.sh --create --command-config ./config/admin.properties --bootstrap-server b-2.testcluster.b.c5.kafka.us-east-1.amazonaws.com:9098 --partitions 3 --replication-factor 3 --topic TestTopic```  
    ```cd -``` #get back to the previous dir  
5. run the producer  
    ```$ cd target && java -jar producer-0.1.0.jar ../config/producer.properties ../conf/input.txt```  
    ```cd -```  #get back to the previous dir  


## Avro Serialized Example

### generate some random data to serialize

1. download avro-tools-${version}.jar  
        ```wget https://repo1.maven.org/maven2/org/apache/avro/avro-tools/1.9.1/avro-tools-1.9.1.jar```  
2. create a valid avro schema, start with  
        ```src/main/resources/avro/com/kafkatools/example/test.avsc```  
3. generate the random lines of data to serialize and packge them in an avro file  
        ```java -jar avro-tools-1.9.1.jar random --schema-file src/main/resources/avro/com/kafkatools/example/test.avsc --count 10 test.avro```  
4. verify the result  
        ```java -jar avro-tools-1.9.1.jar tojson test.avro```  

This will be the test data to serialize in the KafkaAvroProducer
