# System Design Document: Apache Kafka

## 1. Overview
Apache Kafka is a distributed event streaming platform designed for high-throughput, fault-tolerant, and scalable messaging. It is widely used for building real-time data pipelines and streaming applications.

---

## 2. Requirements

### Functional Requirements
- Publish and subscribe to streams of records.
- Store streams of records in a fault-tolerant way.
- Process streams of records in real-time.

### Non-Functional Requirements
- **Scalability**: Handle millions of messages per second.
- **Durability**: Ensure data persistence across broker failures.
- **Low Latency**: Deliver messages in milliseconds.
- **High Availability**: Support replication and failover.

---

## 3. High-Level Architecture

### Components
- **Producer**: Publishes messages to Kafka topics.
- **Consumer**: Subscribes to topics and processes messages.
- **Broker**: Kafka server that stores and serves messages.
- **Topic**: Logical channel to which records are published.
- **Partition**: Sub-division of a topic for parallelism and scalability.
- **ZooKeeper / KRaft**: Manages cluster metadata (ZooKeeper in older versions, KRaft in newer versions).

---

## 4. Data Flow

1. **Producer** sends a message to a Kafka topic.
2. Kafka **Broker** stores the message in the appropriate partition.
3. Messages are replicated across brokers for fault tolerance.
4. **Consumer** subscribes to the topic and reads messages sequentially.
5. Kafka tracks consumer offsets to ensure reliable delivery.

---

## 5. Key Design Considerations

### Partitioning
- Each topic is divided into partitions.
- Messages within a partition are ordered.
- Partitioning enables parallelism and scalability.

### Replication
- Each partition has a leader and multiple followers.
- Followers replicate data from the leader.
- If the leader fails, a follower is promoted.

### Storage
- Kafka stores messages on disk using a **commit log**.
- Messages are retained based on time or size policies.

### Consumer Groups
- Consumers can form groups.
- Each partition is consumed by only one consumer in a group.
- Enables load balancing and parallel consumption.

---

## 6. Sequence Diagram (Textual)

1. Producer publishes message.
2. Broker writes message to partition log.
3. Consumer fetches message from broker.
4. Kafka updates consumer offset.

---

## 7. Technology Choices

- **Language**: Java/Scala (core implementation)
- **Storage**: File system commit log
- **Coordination**: ZooKeeper (legacy) / KRaft (modern)
- **Messaging Model**: Publish-subscribe with partitioned topics
- **Replication**: Leader-follower model

---

## 8. Use Cases

- Real-time analytics pipelines
- Log aggregation
- Event sourcing
- Stream processing (with Kafka Streams or Flink)
- Messaging backbone for microservices

---

## 9. Future Enhancements

- Enhanced KRaft mode to fully replace ZooKeeper.
- Improved tiered storage for infinite retention.
- Native integration with cloud object stores.

---

## 10. Conclusion
Kafka provides a scalable, durable, and high-throughput messaging system. Its partitioned, replicated, and log-based architecture makes it a backbone for real-time data streaming and event-driven systems.