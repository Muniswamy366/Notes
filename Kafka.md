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
partition = hash(key) % number_of_partitions
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
producer.send(new ProducerRecord<>("my-topic", null, "some message"));
```

➡️ Useful for even load balancing, but no message ordering is guaranteed across messages.


