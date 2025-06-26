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
