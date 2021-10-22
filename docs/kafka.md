



### docker

```bash
docker pull wurstmeister/zookeeper
docker pull wurstmeister/kafka

#启动zookeeper，注意端口号，因为以后还有有其他的zookeeper
docker run -d --name kafka-zookeeper -p 2181:2181 --volume /etc/localtime:/etc/localtime wurstmeister/zookeeper:latest      

docker run -d --name kafka -p 9092:9092 --link kafka-zookeeper --env KAFKA_ZOOKEEPER_CONNECT=kafka-zookeeper:2181 --env KAFKA_ADVERTISED_HOST_NAME=localhost --env KAFKA_ADVERTISED_PORT=9092 --volume /etc/localtime:/etc/localtime wurstmeister/kafka:latest              后台启动kafka


docker exec -it <CONTAINER ID> /bin/bash 
cd opt/kafka_2.11-2.0.0/ 

bin/kafka-topics.sh --create --zookeeper kafka-zookeeper:2181 --replication-factor 1 --partitions 1 --topic mykafka

bin/kafka-console-producer.sh --broker-list localhost:9092 --topic mykafka

bin/kafka-topics.sh --list --zookeeper kafka-zookeeper:2181  

bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic mykafka --from-beginning


```



### k8s 

```bash
kubectl create -f k8s/kafka/single/zookeeper-svc.yaml
kubectl create -f k8s/kafka/single/zookeeper-deployment.yaml
```





### helm(因为墙的原因，尽量不用，过于繁琐)

```bash

```

