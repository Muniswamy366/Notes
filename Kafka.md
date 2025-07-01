Apache Kafka is a distributed streaming platform designed to handle high-throughput, low-latency, and fault-tolerant event processing. It works as a publish-subscribe messaging system and also as a distributed commit log.  

What is a Commit Log?  

A commit log (also known as a write-ahead log) is an append-only, ordered sequence of records, where new entries are written at the end and never modified. It is a durable structure—once a message is written, it stays until retention conditions are met.  
```
Partition 0 (Commit Log):

Offset:      0       1       2       3       4       ...
Record:   [msg1]  [msg2]  [msg3]  [msg4]  [msg5]  ...

• New messages always go to the end.
• Consumers read sequentially using offsets.
```

### Core Components
1. Broker
* The heart of Kafka - a server that stores and serves data  
* Each broker is identified by a unique ID  
* Brokers form a cluster and can handle thousands of reads/writes per second  
* Responsible for receiving messages from producers and serving them to consumers  
* The broker instance which we connect to access cluster is also known as bootstrap server.  

2. Topics

* A topic is a logical name to which the records are published
* Topics are split into partitions for scalability and parallelism
* Each topic can have multiple partitions distributed across brokers
* Messages within a partition are ordered by offset

3. Partitions  
* Physical divisions of topics stored on disk
* Enable parallel processing and horizontal scaling
* Each partition is replicated across multiple brokers for fault tolerance
* Messages are appended to partitions in an immutable log structure
* Each partition will have a leader which will take read/write operations to that partition.
* The commit log in Kafka is per partition, not per topic.  

4. Producers  
Applications that publish/send messages to Kafka topics

Can choose which partition to send data to (round-robin, key-based, or custom)

Support batching and compression for efficiency

Receive acknowledgments from brokers to ensure delivery

5. Consumers  
Applications that read/subscribe to messages from topics

Can read from multiple partitions simultaneously

Track their position using offsets

Support both individual and group consumption patterns

6. Consumer Groups  
Multiple consumers working together to process a topic

Each partition is consumed by only one consumer within a group

Enables load balancing and fault tolerance

Automatic rebalancing when consumers join/leave

### Supporting Infrastructure
ZooKeeper (Legacy) / KRaft (New)
ZooKeeper: Manages cluster metadata, leader election, and configuration

KRaft: New consensus protocol replacing ZooKeeper for simpler architecture

Replication
Each partition has one leader and multiple followers

Leader handles all reads/writes for that partition

Followers replicate data from the leader

Automatic failover if leader becomes unavailable

### Key Architectural Benefits
Scalability: Horizontal scaling through partitioning

Durability: Configurable replication and persistence

Performance: High throughput through batching and zero-copy transfers

Fault Tolerance: Automatic failover and data replication

Flexibility: Support for various consumption patterns  

### 📦 Kafka Data Flow

    Producer sends data → to a topic (optionally choosing partition).

    Kafka Broker receives the message and writes it to the correct partition.

    The message is persisted on disk and replicated to other brokers.

    Consumers in a consumer group read from partitions.

    Offsets (position of read messages) are tracked and committed.

### 🔄 Kafka Replication

    Each partition has:

        One leader (handles all reads/writes).

        Zero or more followers (replicas).

    Replication provides fault tolerance.

    If a broker goes down, a replica becomes the new leader.

### 🛠️ Kafka Guarantees

    Durability: Messages are persisted on disk.

    High throughput: Batch compression, zero-copy I/O.

    Scalability: Horizontal scaling using partitions and consumer groups.

    Delivery semantics:

        At-most-once: message might be lost

        At-least-once: message might be duplicated

        Exactly-once: message is delivered only once (requires configuration)

🔷 1. High-Level Components
| Component      | Description                                                                              |
| -------------- | ---------------------------------------------------------------------------------------- |
| **Producer**   | Publishes data (events/messages) to Kafka topics.                                        |
| **Consumer**   | Subscribes to topics and consumes data.                                                  |
| **Topic**      | A named stream of records. Producers write to it, consumers read from it.                |
| **Partition**  | Subdivision of a topic. Each partition is an **ordered, immutable sequence** of records. |
| **Broker**     | A Kafka server. Each broker handles read/write operations for partitions.                |
| **Cluster**    | A group of Kafka brokers.                                                                |
| **Controller** | Special broker that manages partition leader elections and cluster metadata.             |
| **Zookeeper**  | Used for metadata management (being replaced by **KRaft**).                              |

🔷 2. Modern Kafka = KRaft (Kafka Raft Metadata Mode)

Kafka can now run without ZooKeeper using KRaft, which:

    Simplifies deployment (no need for external ZK).

    Uses an internal Raft quorum of controllers to manage metadata.

    All metadata (topics, configs, brokers) stored in Kafka-internal topic @metadata.

    ✅ As of Kafka 3.5+, KRaft is production-ready and the default for new clusters.

🔷 3. Kafka Topic & Partition Design

    A Topic is a named category of messages.

    A Partition is an append-only, ordered log.

    Partitions enable parallelism and scalability.

    Each partition has:

        A leader broker

        Replicas on other brokers (follower replicas)

        A set of in-sync replicas (ISR)

🧠 Example:
```
Topic: user-events
Partitions: 3
Replication factor: 2
→ Total of 6 replicas distributed across brokers
```

🔷 4. Producer Internals

    Can choose partitions (via keys or round-robin).

    Configs:

        acks=all: Wait for all ISR to confirm write

        enable.idempotence=true: Prevent duplicate writes

        compression.type: gzip/snappy/zstd

➕ Idempotent Producer

    Ensures no duplicates during retries.

    Important for exactly-once semantics.

➕ Transactions

    Producers can send messages across multiple partitions atomically.

    Works with transactional.id and Kafka Streams.

🟠 At Least Once:

    A payment message may be replayed after a failure.

    If your consumer isn’t idempotent, the same user may be charged twice.

At Least Once Setup:
```
acks=all
retries=5
enable.idempotence=false (or default)
```

You’ll need to write your consumer logic to handle duplicates.

🟢 Exactly Once:

    Kafka ensures the payment is processed only one time, even if failures happen in the middle.
Exactly Once Setup (Producer + Consumer):
```
acks=all
retries=5
enable.idempotence=true  # Required for exactly once
```

Use Kafka Transactions to commit messages and offsets atomically.

Consumers should read committed data only (isolation.level=read_committed).


🔷 5. Consumer Internals

    Subscribes to one or more topics.

    Belongs to a consumer group.

    Kafka guarantees each partition is read by only one consumer in a group.

    Tracks progress using offsets.

📌 Offset Management

    Auto or manual commit

    Stored in Kafka’s internal topic: __consumer_offsets

➕ Isolation Level

    read_committed – ensures consumers only read committed transactional messages.

🔷 6. Kafka Storage Internals

    Each partition is stored as a set of segment files on disk.

    Old segments can be deleted or compacted (log compaction).

    Efficient use of disk and OS cache.

| Feature            | Description                          |
| ------------------ | ------------------------------------ |
| **Retention**      | Keep messages for X time or size     |
| **Compaction**     | Keep only the latest message per key |
| **Tiered Storage** | Move old segments to cloud/remote FS |

🔷 7. Replication & Fault Tolerance

    Each partition has multiple replicas (leader + followers).

    Leader handles all reads/writes.

    Followers replicate data from the leader.

    In-Sync Replicas (ISR) must acknowledge writes.

💥 If the leader fails:

    Controller triggers a new leader election from the ISR.

🔷 8. Delivery Semantics
| Semantic          | Behavior                       |
| ----------------- | ------------------------------ |
| **At most once**  | Message may be lost (no retry) |
| **At least once** | Message may be duplicated      |
| **Exactly once**  | No loss, no duplicate (EOS)    |

Exactly-once semantics (EOS) needs:

    Idempotent producer

    Transactions

    Proper consumer configuration

🔷 9. Kafka Ecosystem Tools
Tool	Purpose
Kafka Connect	Import/export data between Kafka and systems like DBs, Elasticsearch
Kafka Streams	Java library for real-time stream processing
ksqlDB	SQL-like querying over Kafka topics
Schema Registry	Manages Avro/Protobuf schemas for producers and consumers  

🔷 10. Kafka Cluster Deployment
Minimum Setup:

    3 brokers (for replication and availability)

    (Optional) 3+ ZooKeeper nodes (if not using KRaft)

Production Setup:

    Use Kafka with KRaft mode

    Enable:

        Replication factor ≥ 3

        SSL/TLS + SASL for security

        Monitoring (JMX, Prometheus, Grafana)

✅ Summary
| Area                | Description                             |
| ------------------- | --------------------------------------- |
| **Scalability**     | Horizontal via partitions & brokers     |
| **Durability**      | Replication + disk + ISR                |
| **Performance**     | High throughput via batching, async I/O |
| **Flexibility**     | Pub-sub, stream processing, connectors  |
| **Modern Features** | KRaft, Tiered Storage, EOS              |


Architecture diagram for 3 replicas, 3 brokers, 3 partitions  
![image](https://github.com/user-attachments/assets/71c42c64-0788-44e6-bd3a-6ab29d1dee5d)  
Key Components:

3 Brokers: Each running on different ports (9092, 9093, 9094) with unique IDs
3 Partitions: P0, P1, P2 distributed across brokers with different leaders
3 Replicas: Each partition has one leader and two follower replicas

Replication Strategy:

Partition 0: Leader on Broker 1, Followers on Brokers 2 & 3
Partition 1: Leader on Broker 2, Followers on Brokers 1 & 3
Partition 2: Leader on Broker 3, Followers on Brokers 1 & 2  


Architecture diagram for 2 replicas, 3 brokers, 3 partitions  
![image](https://github.com/user-attachments/assets/d50509f5-889d-482f-8d17-b5d6d9fc2de5)  
Replication Strategy (2 Replicas):

Partition 0: Leader on Broker 1, Follower on Broker 2
Partition 1: Leader on Broker 2, Follower on Broker 3
Partition 2: Leader on Broker 3, Follower on Broker 1

Distribution:

Broker 1: P0 (Leader), P2 (Follower)
Broker 2: P0 (Follower), P1 (Leader)
Broker 3: P1 (Follower), P2 (Leader)  

### How kafka identifies which partition message has to go?
🔷 1. Custom Partition (User-Defined)  

If the producer explicitly specifies the partition number, Kafka uses that.
```
producer.send(new ProducerRecord<>("my-topic", 2, key, value)); // Goes to partition 2
```

🔷 2. Key-Based Partitioning  

If a key is provided, Kafka applies a hashing algorithm to the key to determine the partition.
```
kafkaTemplate.send("my-topic", "user123", "User logged in");
```

Kafka will compute:
```
partition = hash("user123") % numPartitions
```

➡️ This ensures that all messages with the same key go to the same partition, preserving ordering.  

Example:
```
producer.send(new ProducerRecord<>("my-topic", "user-123", "event1"));
```
All events with key user-123 go to the same partition.

🔷 3. Round-Robin (Default when No Key)  

If no partition and no key are specified, Kafka distributes messages in a round-robin fashion across all partitions.
```
kafkaTemplate.send("my-topic", "Some message with no key");
```

➡️ Useful for even load balancing, but no message ordering is guaranteed across messages.

### Queuing vs publish-subscribe in kafka
 1. Queuing Model in Kafka

    You have one topic and one consumer group

    Kafka ensures that each message is delivered to only one consumer instance in that group

    Used for parallel processing and load balancing

🔸 Example:

    Topic: orders

    Group: order-processors

    3 Consumers in group → Kafka distributes messages among them

🔷 2. Publish-Subscribe Model in Kafka

    You have one topic and multiple consumer groups

    Kafka delivers all messages to all groups

    Used for event broadcasting

🔸 Example:

    Topic: user-signups

    Group 1: email-service

    Group 2: analytics-service

✅ Step 1: Producer  
```
@Service
public class SignupProducer {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void publishSignupEvent(String username) {
        kafkaTemplate.send("user-signup", username);
        System.out.println("Produced signup event for user: " + username);
    }
}
```

✅ Step 2: Consumer - Publish/Subscribe Pattern  
```
@Component
public class EmailNotificationConsumer {

    @KafkaListener(topics = "user-signup", groupId = "email-service")
    public void listen(String message) {
        System.out.println("[Email Service] Received: " + message);
    }
}

@Component
public class AnalyticsConsumer {

    @KafkaListener(topics = "user-signup", groupId = "analytics-service")
    public void listen(String message) {
        System.out.println("[Analytics Service] Received: " + message);
    }
}
```
Each of these is in a different consumer group, so both will get every message (Pub/Sub).  
✅ Step 3: Consumer - Queuing Pattern  
```
@Component
public class Worker1 {

    @KafkaListener(topics = "user-signup", groupId = "signup-workers")
    public void listen(String message) {
        System.out.println("[Worker1] Processed: " + message);
    }
}

@Component
public class Worker2 {

    @KafkaListener(topics = "user-signup", groupId = "signup-workers")
    public void listen(String message) {
        System.out.println("[Worker2] Processed: " + message);
    }
}
```
Both are in the same group, so each message will go to only one of them (Queuing pattern).  

### Publish-Subscribe kafka, if two consumers are in single group, how the message are consumed  

Short Answer:

    If **two consumers are in the same consumer group, Kafka will load-balance messages across them — meaning each message is consumed by only one consumer in that group.

So, in this case:

    NOT publish-subscribe

    It's behaving like a queue (i.e., competing consumers)

### Point to Point and queue model same in kafka

Yes, in Kafka, the Point-to-Point model and the Queue model essentially refer to the same concept. They are functionally equivalent, even though the terminology differs slightly depending on context.  

## Interview Questions and Answers

**Apache Kafka Interview Questions and Answers for Tech Leads and Architects**

---

### 1. Explain Kafka architecture in detail.

Kafka is a distributed event streaming platform composed of:

* **Producers** that send records to **topics**.
* **Topics** split into **partitions**, which are distributed across **brokers**.
* **Consumers** that read from topics (usually via **consumer groups**).
* Each partition has one **leader** and optional **replicas** (followers).
* Kafka uses a **commit log** architecture to provide durability and replay.
* **ZooKeeper** (or **KRaft** in newer versions) manages metadata and broker coordination.

---

### 2. What are Kafka’s delivery guarantees?

Kafka offers three message delivery semantics:

* **At most once**: Messages may be lost but never duplicated.
* **At least once**: Messages are not lost, but duplicates may occur.
* **Exactly once**: Messages are neither lost nor duplicated. Achieved using idempotent producers and transactional processing.

---

### 3. How does Kafka achieve high throughput and low latency?

* Uses **sequential disk I/O** with append-only logs.
* **Batching and compression** reduce network overhead.
* **Zero-copy** technology allows efficient file transfer.
* Scales horizontally using **partitions**.

---

### 4. What is ISR (In-Sync Replica)?

ISR is the set of replicas that are fully caught up with the leader. Kafka only commits messages that are acknowledged by all ISRs when `acks=all` is used.

---

### 5. How does Kafka ensure fault tolerance?

* Each partition is replicated across brokers.
* If a broker (leader) fails, an ISR becomes the new leader.
* Kafka Controller handles failover and metadata management.

---

### 6. Explain exactly-once delivery in Kafka.

* **Idempotent producers** prevent duplicate writes.
* **Transactions** ensure atomic writes across partitions.
* **Consumers** use `read_committed` isolation to avoid reading uncommitted/aborted data.
* Offsets and messages are committed in a single transaction.

---

### 7. How does a Kafka consumer group work?

* A **consumer group** allows multiple consumers to share the load.
* Each partition is read by only one consumer in a group.
* Kafka manages **offsets** per group to track progress.

---

### 8. What is log compaction?

Log compaction retains the **latest value** for each key, unlike standard retention that deletes old records. Useful for maintaining the latest state (e.g., user profiles).

---

### 9. How does Kafka scale horizontally?

* Add **brokers** to distribute partitions.
* Increase **partitions** to improve parallelism.
* Add **consumer instances** to share the load within a group.

---

### 10. Describe Kafka’s role in a microservices architecture.

Kafka decouples services by enabling **asynchronous communication**. Each service can produce or consume events independently, improving fault tolerance, resilience, and scalability.

---

### 11. What is Kafka Streams?

Kafka Streams is a Java library for building real-time processing applications:

* Provides windowing, joins, aggregations.
* Works directly on Kafka topics.
* Fully distributed, fault-tolerant.

---

### 12. How does Kafka Connect help in integration?

Kafka Connect is a framework for **source and sink connectors**:

* Moves data between Kafka and external systems (DBs, S3, Elasticsearch).
* Supports configuration-driven deployments.

---

### 13. What is KRaft mode in Kafka?

KRaft (Kafka Raft) mode replaces ZooKeeper by managing metadata within Kafka itself using the Raft consensus protocol. It simplifies deployments and improves scalability.

---

### 14. How does Kafka handle backpressure?

* Consumers control the pace of consumption via `max.poll.records`.
* Use of proper **consumer lag monitoring**.
* Kafka can **pause consumption** or redirect to **dead letter queues** if needed.

---

### 15. Design a real-time fraud detection system using Kafka.

* **Producers** emit transaction events.
* Events are streamed through **Kafka Streams** or **Flink** to apply fraud rules.
* Suspicious events are sent to a **review topic**.
* Alerts are generated and stored in databases or sent to downstream systems.

---


