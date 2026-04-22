# SimpleKafka

A simplified implementation of a distributed messaging system inspired by Apache Kafka.
This project was built to understand how Kafka works under the hood, including brokers, partitions, leader election, and fault tolerance.

---

## 🚀 Demo

![Testing the system with 3 brokers](https://github.com/Muhammed-Abdulbasit/SimpleKafka/blob/main/SimpleKafkaTest.gif)   

### 🧪 What’s happening in the demo

This demo shows a full lifecycle of a distributed system:

1. ZooKeeper is started (cluster coordination)
2. Three brokers are started on different ports
3. A producer sends messages to a topic
4. A consumer reads messages from a partition
5. One broker (the leader) is manually terminated
6. A new leader is elected automatically
7. Producing/consuming from the dead broker fails
8. Producing/consuming from another broker still works
9. The killed broker is restarted and rejoins the cluster

> ⚠️ Note: This implementation does not fully handle automatic failover for consumers, so manual reconnection may be required after a broker failure.

---

## 🧠 What I Learned

* How distributed brokers coordinate using ZooKeeper
* How topics are split into partitions
* How producers distribute messages across partitions
* How consumers read from specific partitions
* How leader election works when a broker fails
* The challenges of fault tolerance and recovery in distributed systems

---

## ⚙️ Setup

### Prerequisites

* Java
* Maven
* ZooKeeper

Install ZooKeeper (Mac):

```bash
brew install zookeeper
```

---

## ▶️ Running the Project

### 1. Build the project

```bash
mvn clean package
```

---

### 2. Start ZooKeeper

```bash
zkServer start
```

---

### 3. Start Brokers (in separate terminals)

```bash
# Broker 1
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.broker.SimpleKafkaBroker 1 localhost 9091 2181

# Broker 2
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.broker.SimpleKafkaBroker 2 localhost 9092 2181

# Broker 3
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.broker.SimpleKafkaBroker 3 localhost 9093 2181
```

---

### 4. Produce Messages (in separate terminal)

```bash
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.client.SimpleKafkaProducer localhost 9091 test-topic
```

---

### 5. Consume Messages (by partition, in seperate terminal. Should be 6th and last terminal)

```bash
# Partition 0
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.client.SimpleKafkaConsumer localhost 9091 test-topic 0

# Partition 1
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.client.SimpleKafkaConsumer localhost 9091 test-topic 1

# Partition 2
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.client.SimpleKafkaConsumer localhost 9091 test-topic 2
```

> 💡 Messages are distributed across partitions, so you won’t see all messages from a single partition.

---

## 💥 Fault Tolerance Test

### Kill a broker

In a broker terminal:

```bash
CTRL + C
```

### Observe:

* A new leader is elected
* Some requests may fail (expected in this implementation)

---

### Produce using another broker

```bash
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.client.SimpleKafkaProducer localhost 9092 test-topic
```

---

### Restart the broker

This is assuming you killed broker 1. If you killed 2 or 3 then use those commands (from step 3) to restart them.

```bash
java -cp target/simple-kafka-1.0-SNAPSHOT.jar com.simplekafka.broker.SimpleKafkaBroker 1 localhost 9091 2181
```

---

## 📁 Data Storage

Data is stored locally in the `data/` directory:

```bash
ls -la data/
```

Structure includes:

* broker directories
* topic directories
* partition logs

---

## ⚠️ Known Limitations

* Consumers do not automatically recover from broker failure
* No replication between brokers
* Minimal error handling and retry logic
* Simplified partitioning strategy

---

## 📌 Future Improvements

* Add consumer retry + failover logic
* Implement replication between brokers
* Improve leader election handling
* Add better logging and monitoring

---

This project was made with the help of https://github.com/buildthingsuseful/build-your-own-kafka