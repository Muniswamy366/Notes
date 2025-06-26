Elasticsearch is a distributed, RESTful search and analytics engine built on Apache Lucene. It's designed for horizontal scalability, real-time search, and complex analytics on large volumes of data.
Elasticsearch is a distributed search and analytics engine used for:

    Full-text search

    Filtering and ranking data

    Log and metrics analysis

    Real-time data exploration

It stores data as JSON documents and allows powerful querying across large datasets, with near real-time speed.  

![image](https://github.com/user-attachments/assets/34543667-86f5-4c14-b589-6d40b8e561b2)  


### Index Structure
**Index:** Collection of documents (similar to a database)
**Document:** Basic unit of information (similar to a row)
**Field:** Key-value pair within a document (similar to a column)
**Mapping:** Schema definition for documents
**Shard:** Horizontal partition of an index
**Replica:** Copy of a shard for redundancy

### Query vs Filter

    Query → Full-text search, scores results (e.g., "laptop bag")

    Filter → Structured data, exact match or range (e.g., price ≤ 1000, category = "bags")

### Why Use Elasticsearch?

    Near real-time full-text search

    Fast filtering and aggregation on massive datasets

    Schema-flexible JSON documents

    Distributed and horizontally scalable

    Rich query DSL

### Elasticsearch vs MongoDB vs Cassandra

| Feature / DB           | **Elasticsearch**                      | **MongoDB**                                  | **Cassandra**                                 |
| ---------------------- | -------------------------------------- | -------------------------------------------- | --------------------------------------------- |
| **Type**               | Search engine (document store)         | Document database                            | Wide-column, distributed database             |
| **Primary Use**        | Full-text search, filtering, analytics | General-purpose app data (JSON)              | High-volume, fast writes, linear scalability  |
| **Query Support**      | Complex, full-text search (Query DSL)  | Rich queries with Mongo Query Language (MQL) | Simple query support (no joins, no full-text) |
| **Schema**             | Semi-structured (JSON)                 | Flexible schema (JSON)                       | Static schema (tables, columns)               |
| **Write Speed**        | Moderate                               | Fast                                         | Extremely fast (optimized for writes)         |
| **Read Speed**         | Fast for search queries                | Fast for normal queries                      | Tunable consistency; fast if well-partitioned |
| **Horizontal Scaling** | Yes (automatic sharding)               | Yes (with replica sets and sharding)         | Yes (excellent, linear scale-out)             |
| **Replication**        | Yes (replica shards)                   | Yes (replica sets)                           | Yes (multi-node replication)                  |
| **Consistency**        | Eventual                               | Tunable                                      | Tunable (default: eventual)                   |
| **Best For**           | Search, logs, analytics                | General apps, content, e-commerce, IoT       | IoT, telemetry, large-scale time-series       |
| **Not Good For**       | OLTP, transactions                     | Full-text search, analytics                  | Search, ad hoc analytics                      |
| **ACID Compliance**    | No                                     | Partial (transactions since v4.0)            | No (eventual consistency)                     |
