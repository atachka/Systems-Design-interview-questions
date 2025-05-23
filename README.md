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

# Caching

1. [What is Caching?](#1-what-is-caching)
2. [How does Caching work?](#1-how-does-caching-work)
3. [What are Eviction Policies(Strategies) for Caching?](#3-what-are-eviction-policiesstrategies-for-caching)
4. [What is CDN(Content Distribution Networks)?](#4-what-is-cdncontent-distribution-networks)

---

# Resiliency

1. [What is Resiliency?](#1-what-is-resiliency)

---

# Scaling Our Data

1. [What are Distributed Storage Solutions?](#1-what-are-distributed-storage-solutions)
2. [What is HDFS Architecture?](#2-what-is-hdfs-architecture)

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

**Normalized Data Example**: In this example We join the customer table with the reservation table, we get customers with name and phone instead of just their ID in reservations.

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

Another approach is to just throw data into text files (csv, json perhaps) into a big distributed storage system like Amazon S3.

- This is called a "data lake"
- Common approach for "big data" and unstructured data.

Another process (i.e., Amazon Glue) creates a schema for that data.
And cloud-based features let you query the data.

- Amazon Athena (serverless)
- Amazon Redshift (distributed data warehouse)
- You still need to think about how to partition the raw data for best performance(by date or having different buckets or directories).

**Example**: In this example AWS Glue monitors the data and structures it to look like a schema,  
than can be read by Amazon Athena or we could issue a SQL query and treat it like a data warehouse

![Data Lake Example](/assets/DataLake-Example.png)

---

### 7. What is ACID compliance?

**Atomicity** : Either the entire transaction succeeds, or the entire thing fails.

**Consistency** : All database rules are enforced, or the entire transaction is rolled back.

**Isolation** : No transaction is affected by any other transaction that is still in progress.

- **Example**: If we have two different commands running, that are fighting over the same data one  
  of them's gonna win, while one command is writing out data another command can't modify that data.

**Durability** : Once a transaction is committed, it stays, even if the system crashes immediately after.

### 8. What is CAP Theorem (Brewer's Theorem)?

**Consistency** :Every read receives the most recent write or an error.

**Availability** : Every request receives a response (it might not be the latest data).

**Partition Tolerance** : The system continues to function despite network failures between nodes.

Even though ACID is good sometimes we have to give up something up in the name of scalability (Modern databases usually give up Availability).

![Cap Theorem](/assets/CAP-Theorem.png)

---

## Caching

### 1. What is Caching?

Caching is the process of storing frequently accessed data in a temporary storage  
layer to improve performance and reduce the need for repeated computations or database  
queries. It helps speed up applications by reducing latency and load on backend systems.

**Example 1**: In this example we hit the database (hitting the disk) every time.

![Caching Example1](/assets/Caching-Example1.png)

**Example 2**: In this example we have a caching layer (a fleet of other servers that is sitting  
alongside our application and their only job is to keep in memory copies of the data that  
has been going in the database, e.g, most recent hits or the popular ones, another advantage  
is that we can scale this fleet independently of our application fleet),

![Caching Example2](/assets/Caching-Example2.png)

### 2. How does Caching work?

- Usually horizontally scaled servers are used.

- Clients hash requests to a given server.

  - Clients(Web/Application Servers) will hash a request for a database call to some given  
    server (apply a mathematical function to the key we are looking for and for a specific  
    key go to a specific caching host, which will be responsible for that segment of data there)

- In-memory (fast).

- Appropriate for applications with more reads than writes.

- The expiration policy dictates how long data is cached. Too long and your data may go stale; too short and the cache won't do much good.

- Hotspots can be a problem (the "celebrity problem", a situation where certain data  
  (like popular posts or users) receives a disproportionately high number of requests, causing performance bottlenecks).

- Cold-start is also a problem. How do you initially warm up (put data in) the cache without bringing down whatever you are caching?
  - One solution is to have a seperate process to warm up the cache before using it.

### 3. What are Eviction Policies(Strategies) for Caching?

Basically it's what cache stays in the cache and what gets removed.

**LRU**: Least Recently Used.

- We evict(remove) the least recently used item from the cache.

**Example**:

In this example when we access something we move it to the head of the Linked List (by doing so we know that item is the most recently used), and the least used item will be at the end automatically to which Tail will point to, if we need to evict something we look at what Tail points to and dispose it.

![LRU Example](/assets/LRU-Example.png)

**LFU**: Least Frequently Used.

- We keep track of how often a given key is it over some period of time, and remove the least hit one.

**FIFO**: First In First Out.

- First thing we put into the cache is the first thing we are going to evict.

A Few Caching Technologies:

![A Few Caching Technologies](/assets/Caching-Technologies.png)

### 4. What is CDN(Content Distribution Networks)?

If we have clients that are distributed all over the world, we need to use CDN because it allows us to distribute the serving of data globally.

- Geographically distributed
- Local hosting of
  - HTML
  - CSS
  - Javascript
  - Images
- Content is hosted on edge locations, which are physically located closer to the end user.
- Some limited computation may be available as well
- Mainly useful for static content, such as images or static web pages. You probably won't be asked to design a static web page, though!

![CDN Example](/assets/CDN-Example.png)

Some CDN providers:

![CDN Providers](/assets/CDN-Providers.png)

---

## Resiliency

### 1. What is Resiliency?

Main goal of Resiliency is to handle and recover from failures gracefully, for example things that can fail:

- A single server
- An entire rack
- An entire data center (AKA "availability zone")
- An entire region
- ...anything more, and you have bigger problems...

Solution Example: If one region is not responding we will reroute to another one, we have to make sure that the new region has the capacity to handle the access traffic.

![Resiliency Data](/assets/Resiliency-Example.png)

Things to consider before distributing servers:

- Secondaries should be spread across multiple racks, availability zones, and regions.
- Make sure your system has enough capacity to survive a failure at any reasonable scale(This means overprovisioning).
- You may need to balance budget vs. availability. Not every system warrants this.
  - Provisioning a new server from an offsite backup might be good enough.
  - Again, ask questions!

---

## Scaling Our Data

### 1. What are Distributed Storage Solutions?

Distributed storage solutions are systems that store data across multiple physical  
machines or locations but make it appear as a single logical storage resource to the user.  
They Must be **scalable**, **available**, **secure**, **fast object storage** and **highly durable**.

**Use cases**: "data lakes", websites, backups, "big data".

Popular Distributed Storage Solutions:

- Amazon S3
  - Pay as you go
  - Different tiers, ie Glacier for archiving is cheaper, but harder to read from. You can also choose the amount of redundancy you need to save money.
  - [Hot / cool / cold storage](https://aws.amazon.com/s3/storage-classes/)
- Google Cloud Storage
- Microsoft Azure
- Hadoop HDFS
  - Typically self-hosted
- Then there are all the consumer-oriented storage solutions
  - Dropbox, Box, Google Drive, iCloud, OneDrive, etc.
  - Generally not relevant to system design

### 2. What is HDFS Architecture?

HDFS (Hadoop Distributed File System) is a distributed file system designed to  
store and manage very large files across multiple machines in a reliable, scalable,  
and fault-tolerant manner. It is a core component of the Hadoop ecosystem.

**How it Works**:

- Files are broken up into blocks, these blocks of data get replicated in our entire cluster.
- Replication is rack-aware, every block we have a copy of it somewhere else(not in the same rack).
- A master "name node" coordinates all operations, it's a node that knows where the blocks of that file are stored and where to get them from.
  - Client will hit the "name node" and name node will come back and direct the client server to the nearest replica.
- Clients try to read from nearest replica.
- Writes get replicated across different racks.
- For high availability, there may be 3 or more name nodes to falli back on, and a highly available data store for metadata.

![HDFS](/assets/HDFS.png)
