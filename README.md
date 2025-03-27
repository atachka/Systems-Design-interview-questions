# System Design Interview Questions

## 📌 Overview

This repository is a collection of system design interview questions and answers based on my research and experience. It serves as a valuable resource for preparing for technical interviews, particularly for senior and architect-level positions. System design is a crucial skill for software engineers, as it involves designing scalable, reliable, and efficient systems—an essential aspect of modern software development.

---

## 🔹 Table of Contents

# Scalability

1. [How can we make our application scalable?](#1-how-can-we-make-our-application-scalable)
2. [Where do servers come from?](#2-where-do-servers-come-from)
3. [What failover strategies (database scaling) are there?](#3-what-failover-strategies-database-scaling-are-there)
4. [What is Sharding (Database Partitioning)?](#4-what-is-sharding-database-partitioning)
5. [What is Denormalizing Data?](#5-what-is-denormalizing-data)
6. [What are Data Lakes?](#6-what-are-data-lakes)
7. [What is ACID compliance?](#7-what-is-acid-compliance)
8. [What is CAP Theorem (Brewer's Theorem)?](#8-what-is-cap-theorem-brewers-theorem)

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

- Resharding means redistributing data, deciding what data goes to the new host that has been added in or what data gets removed from other hosts.

- Hotspots (Celebrity Problem) A situation where certain data (like popular posts or users) receives a disproportionately high number of requests, causing performance bottlenecks.

- Most "NoSQL" databases actually do support most SQL operations and use SQL as their API.

- Still works best with simple key/value lookups.

- A formal schema may not be needed.

- Examples: MongoDB, DynamoDB, Cassandra, HBase.

---

### 5. What is Denormalizing Data?

Normalized Data Example: In this example We join the customer table with the reservation table, we get customers with name and phone instead of just their ID in reservations.

![Normalized Data](/assets/Normalized-Data.png)

#### **Advantages**:

1. **Less storage space**  
   Instead of duplicating Phone and Name on every reservation we just save customer Id.

2. **Updates in one place**  
   If we want to change the users data we change it in one place.

#### **Disdvantages**:

1. **Requiers multiple queries**  
   We have to join tables if we want both reservation and user data.

Denormalized Data Example:

![Denormalized Data](/assets/Denormalized-Data.png)

#### **Advantages**:

1. **Only one query required**  
   No need to do a join to get both reservation and user data.

#### **Disadvantages**:

1. **More storage space**  
   We Duplicatie Phone and Name on every reservation.

2. **Updates are hard**  
   If we want to change the users data we change it in every reservation.

---

### 6. What are Data Lakes?

- Another approach is to just throw data into text files (csv, json perhaps) into a big distributed storage system like Amazon S3.

  - This is called a "data lake"
  - Common approach for "big data" and unstructured data.

- Another process (i.e., Amazon Glue) creates a schema for that data.
- And cloud-based features let you query the data.
  - Amazon Athena (serverless)
  - Amazon Redshift (distributed data warehouse)
  - You still need to think about how to partition the raw data for best performance(by date or having different buckets or directories).

In this example AWS Glue monitors the data and structures it to look like a schema,  
than can be read by Amazon Athena or we could issue a SQL query and treat it like a data warehouse

![Data Lake Example](/assets/DataLake-Example.png)

---

### 7. What is ACID compliance?

**Atomicity** : Either the entire transaction succeeds, or the entire thing fails.

**Consistency** : All database rules are enforced, or the entire transaction is rolled back.

**Isolation** : No transaction is affected by any other transaction that is still in progress.

- Example: If we have two different commands running, that are fighting over the same data one  
  of them's gonna win, while one command is writing out data another command can't modify that data.

**Durability** : Once a transaction is committed, it stays, even if the system crashes immediately after.

### 8. What is CAP Theorem (Brewer's Theorem)?

**Consistency** :Every read receives the most recent write or an error.

**Availability** : Every request receives a response (it might not be the latest data).

**Partition Tolerance** : The system continues to function despite network failures between nodes.

Even though ACID is good sometimes we have to give up something up in the name of scalability (Modern databases usually give up Availability).

![Cap Theorem](/assets/CAP-Theorem.png)
