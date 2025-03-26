# System Design Interview Questions

## 📌 Overview

This repository is a collection of system design interview questions and answers based on my research and experience. It serves as a valuable resource for preparing for technical interviews, particularly for senior and architect-level positions. System design is a crucial skill for software engineers, as it involves designing scalable, reliable, and efficient systems—an essential aspect of modern software development.

---

## Scalability

### 1. How can we make our application scalable?

1. **Vertical Scaling**  
   Increase the power of a single machine by adding more CPU, RAM, or storage. While this can provide immediate performance gains, it has limitations and is usually not ideal for production due to hardware constraints and potential downtime.

2. **Database Separation**  
   Offload the database to a separate server to reduce the load on the application server, especially when handling a high volume of database queries.

3. **Horizontal Scaling with Load Balancing**  
   Distribute incoming traffic across multiple servers using a load balancer. This method enhances reliability and performance by allowing multiple servers to process requests simultaneously.

   - Requires **stateless** web servers, meaning any server should be able to handle any request without relying on stored state from previous requests.
   - Persistent data, such as user sessions, should be stored in a database, cache, or distributed session store.
   - While this approach improves scalability, it introduces additional maintenance complexity.

Choosing the simplest and most effective architecture for your specific needs is key to building a scalable system.

---

### 2. Where Do Servers Come From?

Servers can be provisioned in different ways, depending on infrastructure needs and scalability requirements:

1. **On-Premise Data Centers**  
   Servers are physically hosted and managed within your company's own data centers. This provides full control over hardware and security but requires significant maintenance and upfront investment.

2. **Cloud Services**  
   Virtual machines (VMs) and infrastructure provided by cloud platforms such as:

   - **Amazon EC2** (AWS)
   - **Google Compute Engine** (GCP)
   - **Azure Virtual Machines** (Microsoft Azure)

   These services offer scalability, flexibility, and cost efficiency compared to traditional on-premise servers.

3. **Fully Managed "Serverless" Services**  
   Cloud providers offer managed services that abstract away server management, allowing developers to focus on writing code rather than managing infrastructure. Examples include:
   - **AWS Lambda** (Serverless compute)
   - **Amazon Kinesis** (Real-time data streaming)
   - **Amazon Athena** (Serverless querying)

Each option has trade-offs in terms of cost, performance, and operational complexity. The best choice depends on the specific needs of your application and organization.

---

### 3. What Failover Strategies (Database Scaling) are there?

1. **Cold Standby**  
   When your database fails, have another one on standby to take its place:

   - Database goes down → Restore backup file to standby database → Redirect traffic to the standby database.  
     **Downsides**:
   - Increases downtime because the system can’t run until the new server is provisioned and the backup is restored.
   - Any data that occurred after the last backup will be lost.

2. **Warm Standby**  
   We have another database (read replica), which constantly replicates data from the main database. Many databases have their own replication mechanisms built in, making the read replica always ready to go.  
   **Downsides**:

   - This is essentially vertical scaling because we’re swapping one database with another.

3. **Hot Standby**  
   Instead of just replication, we write data to both databases simultaneously. This is closer to horizontal scaling because we can also read data simultaneously from both databases.

---

### 4. What is Sharding (Database Partitioning)?

Sharded databases are sometimes called "NoSQL". Sharding is a technique used to improve database scalability by distributing data across multiple database instances (shards). Each shard contains a subset of the data, reducing the load on any single database server.

1. **Key-Based Sharding**  
   Data is partitioned based on a specific key (e.g., user ID, region). Requests are directed to the appropriate shard based on this key.

2. **Range-Based Sharding**  
   Data is split based on a predefined range (e.g., customers with IDs 1–100 go to Shard 1, 101–200 go to Shard 2).

3. **Directory-Based Sharding**  
   A lookup table is used to determine which shard contains specific data.

Sharding improves performance and scalability but adds complexity in terms of data consistency and cross-shard queries.

#### **Downsides**:

- Tough to do joins across shards.
- Resharding
- Hotspots
- Most "NoSQL" databases actually do support most SQL operations and use SQL as their API.
- Still works best with simple key/value lookups.
- A formal schema may not be needed.
- Examples: MongoDB, DynamoDB, Cassandra, HBase
