---
title: Database
layout: default
has_toc: true            # 保险起见，也在页面里显式打开
toc_levels: [2,3,4,5]
---

# MySQL || PostgreSQL
- TOC
{:toc}


**to be updated**
### Why Mysql || PostgreSQL？

Four major families of NoSQL databases

*   Columnar storage Hbase
*   K-V storage Redis
*   Image storage Neo4j
*   Document storage MongoDB

Cloud storage OSS

#### Massive Aerospike

Aerospike (abbreviated as AS) is a distributed, scalable NoSQL**database** for key-value storage. It is designed for T-level big data with high concurrency for structured**data storage**,采用 a hybrid architecture where indexes are stored in memory, while data can be stored on mechanical hard drives (HDD) or solid-state drives (SSD). Read and write operations reach microsecond levels, with 99% of responses being achieved within 1 millisecond.

|                      | Aerospike                                                    | Redis                                                       |
| -------------------- | ------------------------------------------------------------ | ----------------------------------------------------------- |
| Type                 | NoSQL database                                               | Cache                                                       |
| Thread count         | Multithreading                                               | Single-threaded                                             |
| Data sharding        | Automated processing is equivalent to sharding               | Provide sharding algorithms, balance data across each shard |
| Data expansion       | Dynamically increase data volume to balance traffic          | Need to shut down                                           |
| Data synchronization | After setting the replication factor, failover can be completed transparently | Manual failover and data synchronization                    |
| Carrier              | Memory storage index + SSD storage data                      | Memory                                                      |

Aerospike, as a high-capacity NoSql solution, is suitable for scenarios with relatively high capacity requirements and lower QPS. It is primarily used in the advertising industry. Personalized advertising recommendations are based on understanding consumers' unique preferences and habits, enabling accurate prediction or guidance of their purchasing needs. This involves presenting highly relevant ads to consumers at the right place, right time, and in the right format to promote their consumption behavior.

(ETL data warehouse technology) Extract (extract), Transform (transform), Load (load)

* The user behavior log collection system collects logs and pushes them to ETL for data cleaning and transformation

* The data after ETL is sent to the recommendation engine to calculate the recommendation results for each consumer, where the recommendation logic includes both rules and algorithms

* Collect user features such as recent browsing history and longest stay, and analyze algorithms for product similarity, user similarity, and similarity.

* Store the results of the recommendation engine in the Aerospike cluster and provide them to the ad delivery engine for real-time retrieval.

  Perform offline and real-time analysis of logs through HDFS and HBase respectively, then store the tag results (tag: programmer, otaku...) of user profiles into the high-performance NoSQL database Aerospike, while backing up the data to a geographically distant data center. Front-end ad delivery requests read the corresponding user profile data from the user profile database through the decision engine (delivery engine), and then bid based on the bidding algorithm. After a successful bid, the ad can be displayed. And after a successful bid, what specific ad to display to the user is completed by the personalized recommendation ads mentioned above.

|               | Aerospike   | Mysql    |
| ------------- | ----------- | -------- |
| Database Name | Namespace   | Database |
| Table Name    | Set         | Table    |
| Record        | Bin         | Column   |
| Field         | Record      | Row      |
| Index         | key, pk, kv | pk       |

#### Graph Neo4j

> Neo4j is an open-source NoSQL database developed in Java, which stores structured data in a graph rather than in tables. It is an embedded, disk-based, fully transactional Java persistence engine. Program data is stored in an object-oriented, flexible network structure rather than in strict tables, but it still offers all the benefits of a fully transactional, enterprise-grade database.

A graph-based data structure composed of nodes (Node) and edges (Edge). Nodes represent entities, identified by a globally unique ID, and edges represent relationships used to connect two nodes. In simple terms, a knowledge graph is a relationship network formed by connecting all kinds of information. Knowledge graphs provide the ability to analyze problems from a "relationship" perspective.

In the context of the internet and big data, search engines like Google, Baidu, and Sogou have created their own knowledge graphs based on this background: **Knowledge Graph (Google)**, **Zhixin (Baidu)**, and **Zhili Fangtu (Sogou)**, primarily aimed at improving search quality.

The main purpose of my project is for friend recommendation. A graph database refers to a database that stores and queries data in the form of a graph data structure. In a relational graph, the organization of relationships adopts a graph structure, making it highly suitable for storage using a graph library.

Summary of Advantages:

*   In terms of performance, using CQL queries, the speed of querying long-range relationships is fast.

*   Good at discovering hidden relationships, for example, by determining whether there is a path between two points on a graph, one can discover the connections between things.

#### **Document MongoDB**

> MongoDB is a database based on distributed file storage, which is the most feature-rich NoSQL database that resembles a relational database. Under high load conditions, server performance can be guaranteed by adding more nodes. Written in C++, it provides a scalable, high-performance, and easy-to-deploy data storage solution for web applications.

**What is BSON**

> {key:value, key2:value2} is similar to JSON, and is a binary storage format that supports embedded document objects and array objects. However, BSON has some data types that JSON does not, such as value types including string, double, Array, Date. BSON can be used as a storage format for network data exchange. Its advantages are high flexibility, but its disadvantage is not very ideal in terms of space utilization.

BSON has three characteristics: lightweight, traversable, and efficient

```mysql
/* Query: The find() method can accept multiple keys, separated by commas */
db.collection.find({ key1: value1, key2: value2 }).pretty();

/* Update:
   $set   – set the value of a field
   $unset – remove a specified field
   $inc   – increment the value of a field
*/
db.collection.update(
  { where },
  { $set: { fieldName: value } },
  { multi: true }
);

/* Delete:
   justOne – if true, delete only one matching document (default is false, delete all matching documents)
*/
db.collection.remove(
  { where },
  { justOne: <boolean>, writeConcern: <writeConcern> }
);
```

**Advantages:**

* **The way documents are stored, making it more convenient to access data.**

  For a hierarchical data structure, using a flat, tabular structure to query and store data is very difficult.

* **Built-in GridFS, supports large-capacity storage.**

  GridFS is an excellent distributed file system that supports massive data storage and meets fast range queries for large datasets.

* **Superior performance**

  With millions of document objects and nearly 10GB of data, queries on indexed IDs will not be slower than MySQL, while queries on non-indexed fields are completely superior. MySQL cannot actually handle queries on any fields under large data volumes, while MongoDB's query performance is truly impressive. Write performance is equally satisfying, and writing millions of level data, MongoDB can basically solve it in less than 10 minutes.

Disadvantages:

*   Does not support transactions
*   Large disk space usage

MySQL 8.0 version

**1\. Performance**: The speed of MySQL 8.0 is twice as fast as MySQL 5.7.

**2\. NoSQL**: MySQL started providing NoSQL storage features from the 5.7 version, and in the 8.0 version, NoSQL has been further improved.

**3\. Window Functions**: Implement several new query methods. Window functions are similar to aggregate functions like SUM() and COUNT(), but they do not combine multiple rows of query results into a single row. Instead, they return the results in multiple rows, meaning window functions do not require GROUP BY.

**4\. Hidden Index**: In MySQL 8.0, indexes can be "hidden" and "shown." When an index is hidden, it is not used by the query optimizer. We can use this feature for performance debugging, for example, by hiding an index first and then observing its impact on the database. If the database performance decreases, it indicates that the index is useful, and you can then "restore it to show." If the database performance shows no change, the index is redundant and can be considered for deletion.

#### **Cloud storage**

|             | OSS                                                          | Self-built                                                   |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Reliability | Availability not less than 99.995%Data design durability is no less than 99.9999999999% (12 nines) | Constrained by hardware reliability, prone to issues, once disk bad sectors occur, irreversible data loss is likely to happen. Manual data recovery is difficult, time-consuming, and labor-intensive. |
| Security    | Server-side encryption, client-side encryption, anti-hotlinking, IP black/white lists, etc. Multi-user resource isolation mechanism, supports cross-regional disaster recovery mechanism. | Need to purchase additional cleaning and black hole devices. Need to implement security mechanisms separately. |
| Cost        | Multi-line BGP backbone network, no bandwidth limit, upstream traffic is free. No need for operation and maintenance personnel or hosting fees, 0 cost operation and maintenance. | Single-line or dual-line access is slow, has bandwidth limits, requires manual expansion during peak periods. Requires dedicated operation and maintenance personnel, high cost. |

**Steps to Use**

1、Enable the service

2、Create storage space

3、Upload files, download files, delete files

4、Domain binding, log recording

5、Authorization access based on open interfaces

**Function**

Image editing (cropping, blurring, watermark)

Video Screenshot

Audio transcoding, video repair

**CDN acceleration**

Object storage OSS combined with Alibaba Cloud CDN service can optimize scenarios where downloads of static hot files are accelerated (i.e., scenarios where a large number of users in the same region download the same static file simultaneously). The storage space (Bucket) of OSS can be used as the origin server, and Alibaba Cloud CDN can be used to publish the source content to edge nodes. When a large number of terminal users repeatedly access the same file, they can directly obtain the cached data from the edge nodes, improving access response speed

#### **FastDFS**

> **A lightweight open-source distributed file system**. It manages files, with functions including: **file storage, file synchronization, file access** (file upload, file download), etc., solving the problems of **large-capacity storage and load balancing**. It is easy to set up a high-performance file server cluster using FastDFS to provide file upload, download, and other services. Such as **photo album websites, video websites**, etc.

**Scalability:** Supports horizontal expansion, can be dynamically scaled;

**High Availability:** First is the availability of the entire file system, and second is the integrity and consistency of the data;

**Flexible Storage:** Resources in the storage pool can be flexibly added or removed according to business needs without interrupting system operation

Features

*   Seamlessly integrates with popular web servers, FastDFS has provided Apache and Nginx extension modules
*   File IDs are generated by FastDFS as file access credentials, and FastDFS does not require a traditional name server
*   Grouped storage, flexible and concise, peer-to-peer structure, no single point of failure
*   Files are not chunked, uploaded files correspond one-to-one with files in the OS file system
*   Both small and medium files are well-supported, supports massive storage of small files
*   Supports storing only one copy of files with identical content, saving disk space
*   Supports multiple disks, supports single disk data recovery
*   Supports online expansion, supports master-slave files
*   File download supports multi-threading, supports resuming from breakpoint

**Composition**

* **Client**

  Interacts with tracker servers or storage nodes for data exchange via proprietary interfaces using the TCP/IP protocol.

* **Tracker**

  The tracker server's role is load balancing and scheduling. During file uploads, it can find the address for file upload based on policies. Trackers perform load balancing in access.

* **Storage node (storage)**

  The role of Storageserver is file storage, and files uploaded by clients are ultimately stored on the Storage server. The Storage server**does not implement its own file system but instead uses the operating system's file system to manage files**. All servers in the storage nodes can**be added or taken offline at any time without affecting online services**.

**Resumable Upload**

The MD5 of the file size involved in continuation upload will not change. The continuation upload process is similar to file upload, first**locating the source storage**, completing a full or partial upload, and then **synchronizing server files within the same group through binlog**.

**Configuration Optimization**

Configuration files: tracker.conf and storage.conf

```java
// FastDFS uses a memory pool approach.
// Since version 5.04, incremental preallocation is used:
//   - Tracker preallocates 1024 connections at a time
//   - Storage preallocates 256 connections at a time
max_connections = 10240
// Set max_connections to a sufficiently large number based on actual needs,
// for example 10240 or even higher.
// At the same time, increase the maximum number of open files allowed per process.
vi /etc/security/limits.conf   // Changes take effect after system reboot
* soft nofile 65535
* hard nofile 65535
```

```java
work_threads = 4
// Note: To avoid CPU context-switch overhead and unnecessary resource consumption,
// it is not recommended to set this parameter too high.
// Formula: work_threads + (reader_threads + writer_threads) = number of CPU cores
```

```java
// For single-disk mount configurations, set both disk read and write threads to 1.
// If the disk is configured with RAID, consider increasing the number of read/write threads
// to maximize disk performance.

disk_rw_separated: Whether to separate disk read and write operations
disk_reader_threads: Number of read threads per disk
disk_writer_threads: Number of write threads per disk
```

**Avoid duplication**

How to avoid duplicate file uploads Solution After the file is uploaded successfully, calculate the MD5 corresponding to the file and then **store it in MySQL**. When adding a file, compare the **file MD5 with the stored information in MySQL**. DigestUtils.md5DigestAsHex(bytes).

### Transactions

#### **1、The four characteristics of a transaction**

**Four Major Characteristics of Transactions:** Atomicity, Consistency, Isolation, Durability

**Atomicity:** A transaction is the smallest execution unit and cannot be split. The atomicity of a transaction ensures that actions are either fully completed or not executed at all.

**Consistency:** Before and after executing a transaction, the data remains consistent. The results of multiple transactions reading the same data are the same;

**Isolation:** When accessing the database concurrently, one user's transaction is not interfered with by other transactions. The database is independent between concurrent transactions;

**Durability:** After a transaction is committed, the changes it makes to the data in the database are permanent and should not be affected by any database failures.

**Implementation guarantees:**

MySQL's storage engine InnoDB uses the redo log to guarantee consistency and durability, the undo log to guarantee atomicity, and various locks to guarantee isolation.

#### **2、Transaction Isolation Levels**

**Read Uncommitted**

- **Definition:** The lowest isolation level. Transactions can read uncommitted changes from other transactions (dirty reads).
- **MySQL (InnoDB):** Supports it, but internally still reads committed data for consistent reads—dirty reads are rare in practice.
- **PostgreSQL:** Mapped to **Read Committed** internally; dirty reads are not possible.

**Read Committed**

- **Definition:** Transactions only see data committed before each statement starts; prevents dirty reads, but non-repeatable reads and phantom reads may occur.
- **MySQL (InnoDB):** Uses *record* and *gap locks* only when necessary; range queries can still see phantoms.
- **PostgreSQL (default):** Each statement gets a fresh snapshot; no gap locks—phantoms possible.

**Repeatable Read**

- **Definition:** All statements in a transaction see the same snapshot of data, preventing dirty and non-repeatable reads. Phantom reads are still possible under the SQL standard.
- **MySQL (InnoDB default):** Prevents phantoms by using **next-key locks** (row + gap), which may block concurrent inserts/updates in scanned ranges.
- **PostgreSQL:** Implements **snapshot isolation**—phantoms are invisible within the transaction’s snapshot, but *write skew* anomalies are possible. To eliminate them, use SERIALIZABLE.

**Serializable**

- **Definition:** The strictest isolation; transactions behave as if executed one by one in sequence. Prevents all anomalies.
- **MySQL (InnoDB):** Emulated using locking; can significantly reduce concurrency.
- **PostgreSQL:** Adds **predicate locks** and detects dangerous patterns; aborts conflicting transactions with `serialization_failure` (SQLSTATE 40001), requiring retry.

| Isolation Level  | SQL Standard Possible Issues         | MySQL (InnoDB) Behavior                | PostgreSQL Behavior                                       |
| ---------------- | ------------------------------------ | -------------------------------------- | --------------------------------------------------------- |
| Read Uncommitted | Dirty, non-repeatable, phantom reads | Dirty reads rare; behaves close to RC  | Internally RC; no dirty reads                             |
| Read Committed   | Non-repeatable, phantom reads        | Phantoms possible, gap locks on demand | Phantoms possible, no gap locks                           |
| Repeatable Read  | Phantom reads possible               | Blocks phantoms via next-key locks     | Snapshot isolation (no phantoms, but write skew possible) |
| Serializable     | None                                 | Lock-based serial execution            | Serializable snapshot isolation + retries                 |

#### **3、Default Isolation Level - RR**

**MySQL (InnoDB) default:** **REPEATABLE READ** (RR)

- Guarantees the same snapshot for the whole transaction—multiple reads of the same row return consistent data unless modified by the transaction itself.
- Prevents phantom reads by **locking index ranges** with **gap locks** and **next-key locks** during current reads.
- Concurrency can be reduced due to blocking inserts/updates in scanned ranges.

**PostgreSQL default:** **READ COMMITTED**

- **REPEATABLE READ** in PostgreSQL is **snapshot isolation**:
    - Multiple reads within the transaction see the same snapshot.
    - Phantoms are invisible, but **write skew anomalies** are possible.
    - No gap locks—concurrent inserts in scanned ranges are allowed, but they won’t appear in the current transaction’s view.
- **SERIALIZABLE** is required to eliminate write skew, with predicate locks + retry on conflict.

---

**To ensure absolute safety:**

- MySQL: Set isolation level to **SERIALIZABLE** to execute transactions logically sequentially (full ACID), at the cost of performance.
- PostgreSQL: Use **SERIALIZABLE** for the same guarantee—may result in `serialization_failure` (SQLSTATE 40001) requiring retries.

---

##### Three practical approaches

1. **MVCC + version field** (for snapshot-read phantom prevention)

    - Works in both MySQL and PostgreSQL; only allows one update per version.

    - Example:

      ```sql
      SELECT id FROM table_xx WHERE id = ? AND version = V;
      UPDATE table_xx SET version = V+1 WHERE id = ? AND version = V;
      ```

2. **Gap Lock + Next-Key Lock** (for current-read phantom prevention)

    - **MySQL only**: Locks the index range to block concurrent inserts/updates.

    - Example:

      ```mysql
      SELECT id FROM table_xx WHERE id > 100 FOR UPDATE;
      SELECT id FROM table_xx WHERE id > 100 LOCK IN SHARE MODE;
      ```

    - **PostgreSQL**: Does not use gap locks; to prevent anomalies in current reads, escalate to `SERIALIZABLE` or use explicit advisory locks.

3. **Serializable isolation level**

    - Both databases: Eliminates all phantom/read anomalies.
    - MySQL: Lock-based serialization.
    - PostgreSQL: Predicate locks + conflict detection, requiring retries.

#### **4、RR and RC Use Cases**

The transaction isolation levels RC (read commit) and RR (repeatable read) are implemented based on Multi-Version Concurrency Control (MVCC).

|                | RC                                                           | RR                                                    |
| -------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| Implementation | Multiple query statements will create multiple different ReadViews | Only one version of ReadView is needed                |
| Granularity    | Statement-level read consistency                             | Transaction-level read consistency                    |
| Accuracy       | Data at the point of execution of each statement             | Data at the point of execution of the first statement |



#### **5、Row Locks, Table Locks, Intention Locks**

**MySQL (InnoDB) supports row-level locking and table-level locking, with row-level locking as the default.**

InnoDB categorizes locks as follows:

- **Row-level locks**
    - **Record Lock:** Locks index entries (the rows that match the criteria). Other transactions cannot modify or delete the locked rows.
    - **Gap Lock:** Locks the "gaps" between index entries, preventing inserts into the range (does not lock existing rows). Used to prevent phantoms under `REPEATABLE READ`.
    - **Next-key Lock:** Combination of record lock + gap lock; locks both the matching rows and the index range.
- **Table-level locks**
    - Locks the entire table for the current operation. Coarse granularity → lower concurrency but simpler to implement, lower resource cost, and avoids deadlocks.
- **Intention locks** (table-level metadata locks)
    - **IS (Intention Shared):** Intends to acquire S locks on certain rows.
    - **IX (Intention Exclusive):** Intends to acquire X locks on certain rows.

| Mutual exclusivity       | Shared lock (S) | Exclusive lock (X) | Intent shared lock IS | Intent exclusive lock IX |
| ------------------------ | --------------- | ------------------ | --------------------- | ------------------------ |
| Shared lock (S)          | ✅               | ❌                  | ✅                     | ❌                        |
| Exclusive lock (X)       | ❌               | ❌                  | ❌                     | ❌                        |
| Intent shared lock IS    | ✅               | ❌                  | ✅                     | ✅                        |
| Intent exclusive lock IX | ❌               | ❌                  | ✅                     | ✅                        |

---

**PostgreSQL locking model**

PostgreSQL also supports **row-level** and **table-level** locks, but differs in important ways:

- **No gap locks:** PostgreSQL’s MVCC does not use gap locks to prevent phantoms; instead, phantoms are avoided via snapshot isolation or predicate locks in `SERIALIZABLE` mode.
- **Row-level locks**:
    - `FOR UPDATE` / `FOR NO KEY UPDATE` → Exclusive row lock for updates.
    - `FOR SHARE` / `FOR KEY SHARE` → Shared row lock for read or foreign key checks.
    - Locks are **held until transaction end**.
- **Table-level locks** (acquired via `LOCK TABLE ...`):
    - AccessShareLock (lowest level, e.g., `SELECT`)
    - RowShareLock, RowExclusiveLock, ShareLock, ShareRowExclusiveLock, ExclusiveLock, AccessExclusiveLock (strongest, blocks all)
    - These are **automatically managed** by the system or can be explicitly requested.
- **No intention lock concept** like InnoDB’s IS/IX; PostgreSQL manages lock compatibility internally using a different lock manager architecture.
- **Predicate locks (SERIALIZABLE mode)**: Logical locks on query predicates to detect conflicts and prevent anomalies without blocking like gap locks.

---

**Key differences summary**

| Feature                 | MySQL (InnoDB)                       | PostgreSQL                                                   |
| ----------------------- | ------------------------------------ | ------------------------------------------------------------ |
| Gap locks               | Yes (REPEATABLE READ / SERIALIZABLE) | No (phantoms avoided via MVCC or SERIALIZABLE predicate locks) |
| Next-key locks          | Yes                                  | No                                                           |
| Intention locks (IS/IX) | Yes, explicit compatibility matrix   | No explicit IS/IX, internal lock manager handles hierarchy   |
| Row lock types          | Record, Gap, Next-key                | FOR UPDATE / FOR NO KEY UPDATE / FOR SHARE / FOR KEY SHARE   |
| Table lock granularity  | Explicit IS/IX + S/X                 | AccessShare → AccessExclusive hierarchy                      |
| Phantom prevention      | Locking (gap/next-key locks)         | Snapshot isolation or SERIALIZABLE predicate locks           |

---

**Takeaway:**

- MySQL’s concurrency control at RR relies heavily on **physical range locks** (gap/next-key).
- PostgreSQL prefers **non-blocking snapshot reads** and detects anomalies in SERIALIZABLE mode without blocking range inserts—conflicts result in retries instead of waits.



#### **6、MVCC Multi-Version Concurrency Control**

MVCC is a multi-version concurrency control mechanism that allows transactions to see the data they expect through visibility, reducing system overhead. (Works at RC and RR levels)

InnoDB's MVCC, by saving the system version number (can be understood as the transaction ID) after each row record, automatically increments the system version number whenever a new transaction starts, and the system version number at the time the transaction starts becomes the transaction ID. This ensures that the rows read by a transaction are either those that already existed before the transaction started, or those inserted or modified by the transaction itself, preventing the occurrence of phantom reads.

1. MVCC methods only apply to the Read Committed and Repeatable Read isolation levels in MySQL.

2\. Read uncommitted cannot be used because of dirty reads, which means it can read data rows from uncommitted transactions, so it is not applicable to MVCC.

3\. Simple SELECT statements do not require locking, while delete, update, and SELECT FOR UPDATE scenarios that require current reading will involve locking.

The reason is that the creation and deletion versions in MVCC only occur after a transaction is committed. Objectively, MySQL uses an optimistic locking implementation, where each row has a version number, and the save operation determines success based on the version number. InnoDB's MVCC uses snapshot storage in the Undo log, which connects all snapshots of a data row through rollback pointers.

**Version chain**

In InnoDB engine tables, there are two necessary hidden columns in its clustered index records:

**trx\_id**

This ID is used to store the transaction ID of each modification to a clustered index record.

**roll\_pointer**

Whenever a modification is made to a clustered index record, the old version is written into the undo log. This roll\_pointer is a pointer that stores the position of the previous version of the clustered index record, allowing access to the information of the previous version through it. (Note that insert operations' undo logs do not have this attribute because they do not have an old version.)

Every modification is recorded in the version chain. **SELECT can retrieve records from the version chain, which achieves concurrent execution of read-write and write-read, thereby enhancing system performance.**

### Indexes (wait for supplement)

#### **1、Innodb and Myisam engines**

**Myisam:** Supports table-level locking, suitable for read-intensive scenarios, does not support foreign keys, does not support transactions, and indexes are stored in separate files from data

**InnoDB:** Supports row and table locks, with row locks as the default, suitable for concurrent scenarios, supports foreign keys, supports transactions, and indexes are in the same file as data

#### **2、Hash index**

Hash indexes use the value of the indexed column to calculate its hashCode, then store the physical location of the row data at the corresponding position of the hashCode. Because they use a hashing algorithm, access speed is very fast. However, one value can only correspond to one hashCode, and due to the hashing distribution method, hash indexes do not support range searches or sorting functions

#### **3、B+ Tree Index**

**Advantages:**

B-trees have low disk read/write costs, fewer query times, more stable query efficiency, and are beneficial for database scanning.

B+ tree is an upgraded version of B tree, where only leaf nodes store data and other nodes are used for indexing. Index nodes can be fully loaded into memory to increase query efficiency. Leaf nodes can be implemented as doubly linked lists, thus **improving the efficiency of range searches and expanding the range of the index**

When dealing with large-scale data storage, red-black trees often suffer from excessive disk IO read and write operations due to **excessive tree depth**, which can lead to low efficiency. Therefore, as long as we use a better tree structure to reduce the height of the tree, B trees and B+ trees can have multiple children, ranging from dozens to thousands, which can reduce the height of the tree.

**Principle of Disk Prefetching**: Set the size of a node to be equal to one page, so that each node only needs one I/O operation to load completely. To achieve this purpose, the following techniques are required in the actual implementation of B-Trees: When a new node is created, directly allocate space for one page, which ensures that **one node is physically stored in one page**. Combined with the fact that computer storage allocation is always aligned by pages, it achieves the goal of one node requiring only one I/O operation.

#### 4\. Creating indexes

```sql
CREATE [UNIQUE | FULLTEXT] INDEX index_name 
ON table_name (column_name) [USING index_method];

Explanation:
UNIQUE    – Optional. Specifies that the index is a unique index.
FULLTEXT  – Optional. Specifies that the index is a full-text index.
INDEX / KEY – Either can be used to declare an index on a field; both serve the same purpose.
index_name – Optional. The name to assign to the created index.
column_name – The name of the column to be indexed; it must be a column already defined in the table.
Note: The default index method is B+TREE.
```

#### **5、Clustered Index and Non-Clustered Index**

**Clustered Index:** Stores data and indexes in one place, with the leaf nodes of the index structure saving row data (**Primary Key Index**)

**Non-Clustered Index:** Stores data and indexes separately, with the leaf nodes of the index structure pointing to the corresponding data locations (**Secondary Index**)

The leaf nodes of a clustered index are the data nodes, whereas the leaf nodes of a non-clustered index are still index nodes, but they contain pointers to the corresponding data blocks.

#### 6\. The most left prefix problem

The most left prefix principle is primarily used in composite indexes, where the B+Tree of a composite index is arranged based on the first key.

The underlying structure of a composite index is a B+ tree, but the nodes of the B+ tree for a composite index store keys. Since building a B+ tree can only determine index relationships based on a single value, the database relies on the leftmost field of the composite index to construct it.

Using operators like >, <, etc., for matching will prevent the subsequent columns from using the index, because the data matched through these methods is unknown.

### SQL query

#### **1\. Execution Process of SQL Statements**

**Query statement:**

```mysql
select * from student  A where A.age='18' and A.name='张三';
```

Combining the above explanation, let's analyze the execution flow of this statement:

① Establish a connection with MySQL through the client/server communication protocol and check for permissions.

② For MySQL versions before 8.0, check if caching is enabled. If the Query Cache is enabled and a completely identical SQL statement is hit, the query result is returned directly to the client;

③ The parser performs syntax and semantic parsing and generates a parse tree. If the query is select, the table name is tb\_student, and the condition is id='1'

④ The query optimizer generates an execution plan. It checks whether optimization is possible based on indexes.

⑤ The query execution engine executes the SQL statement and obtains the query results based on the storage engine type. If Query Cache is enabled, it caches the results; otherwise, it returns them directly.

#### **2\. Subquery and Covering Index**

**Regular Index** (Unique Index + Composite Index + Full-Text Index) requires scanning the index tree twice

(1) First, locate the primary key value id=5 through the regular index;

(2) Then, locate the row records through the clustered index;

This is what is known as **reverse table lookup**, first locate the primary key value, then locate the row record, and its performance is lower than scanning the entire index tree.

**Covering index**: Primary key index == Clustered index == Covering index

If the columns in the WHERE condition and the returned data are in one index, then there is no need to look back at the table, which is called a covering index.

**Implement Covering Index**: A common method is to add the fields to be queried to the composite index.

#### 3\. Explain and Optimize

Reference: [https://www.jianshu.com/p/8fab76bbf448](https://www.jianshu.com/p/8fab76bbf448)

```mysql
mysql> explain select * from staff;
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra |
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
|  1 | SIMPLE      | staff | ALL  | NULL          | 索引  | NULL    | NULL |    2 | NULL  |
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
1 row in set
```

**Index Optimization:**

① Leftmost Prefix Index: 'like' is only used with 'string%', and '=' and 'in' in the statement will dynamically adjust the order.

② Unique Index: The uniqueness of the unique key is above 0.1.

③ Cannot use index: !=, is null, or, ><, (**5.7 and later automatically determine based on quantity**) in, not in

④ Composite index: Avoid using select \*, use covering index for query columns

```mysql
SELECT uid From user Where gid = 2 order by ctime asc limit 10
ALTER TABLE user add index idx_gid_ctime_uid(gid,ctime,uid) 
```

**Statement optimization:**

① char fixed-length queries are efficient, varchar records data length in the first byte

② Indexes should be added to Rows in Explain

③ Both group by and order by fields involve indexes

④ Pagination in Limit will slow down as the start value increases; use subqueries + table joins to solve

```sql
select * from mytbl order by id limit 100000,10  改进后的SQL语句如下：
select * from mytbl where id >= ( select id from mytbl order by id limit 100000,1 ) limit 10
select * from mytbl inner ori join (select id from mytbl order by id limit 100000,10) as tmp on tmp.id=ori.id;
```

⑤ count performs a full table scan; if estimation is possible, use explain

⑥ When deleting a table, it generates a large amount of undo and redo logs; use truncate to determine deletion

**Table structure optimization:**

① No more than 200 tables in a single database

② No more than 5 million data points in a single table

③ A single table should not exceed 40 columns

④ A single table index should not exceed 5

**Database Normalization**:

① First Normal Form (1NF) columns cannot be divided

② Second Normal Form (2NF) attributes are fully dependent on the primary key \[Eliminating partial functional dependencies\]

③ Third Normal Form (3NF) attributes do not depend on other non-prime attributes \[Eliminating transitive dependencies\]

**Configuration Optimization:**

Configure connection number, disable Swap, increase memory, upgrade SSD hard drive

#### 4、JOIN queries

![](https://image-static.segmentfault.com/276/780/2767807589-5c122586a23c4_articlex)

**left join (left outer join)** returns all records from the left table and the records from the right table where the associated fields are equal

**right join (right outer join)** returns all records from the right table and the records from the left table where the associated fields are equal

**inner join (equi-join)** only returns rows where the associated fields in the two tables are equal

### **Cluster**

#### 1\. Master-slave replication process

**MySQL Master-Slave Replication:**

*   **Principle**: Copy the binlog logs from the master server to the slave server to execute them, achieving data consistency between the master and slave.
*   **Process**: Start an I/O thread from the slave to request Binlog logs from the master. The master node starts a binlog dump thread, checks its binary logs, and sends them to the slave. The slave saves the received data to the relay log (Relay log) and also starts an SQL thread to execute the operations in the Relay on its own machine.
*   **Pros**:
    *   As a standby database and without affecting business
    *   Can be set up for read-write separation, one write database, and one or more read databases on different servers, to fully leverage server and database performance, but ensuring data consistency

**binlog record format:** statement, row, mixed

Statement-based replication, row-based replication, and mixed (statement and row) replication. Among them, row-based replication is better at ensuring data consistency between the master and slave databases, but it generates more logs, so consider disk space when setting it up

#### 2、Data consistency issues

"Master-slave replication has latency," during this latency period, if you read from the slave database, you might read inconsistent data.

**Cache record write key method:**

Record in the cache which records have had write requests to route between reading the master database or the slave database

**Asynchronous Replication:**

In asynchronous replication, after the master completes an operation and writes to the binlog log, it returns to the client, and this action ends without verifying whether the slave has received it or not, which may **cause data inconsistency**.

**Semi-synchronous Replication:**

When the master database commits a transaction, it does not return immediately but waits for one of the slave databases to receive the Binlog and successfully write it to the Relay-log before returning to the client. By having one copy of the Binlog on the master database and another on the Relay-log of one of the slave databases, data security and consistency are guaranteed.

**Full synchronous replication:**

In full synchronization replication, when the master completes a transaction, it only returns to the client after all slaves have executed that transaction. Because it needs to wait for all slaves to execute the transaction before returning, the performance of full synchronization replication will inevitably **be severely affected**.

#### 3、Cluster architecture

**Keepalived + VIP + MySQL Master-Slave/Active-Active**

When the write node Master db1 fails, the switch script is triggered by MMM Monitor or Keepalived to migrate the VIP to the available Master db2. During network jitter or network partitioning, MMM Monitor may make incorrect judgments, and in severe cases, the write VIP switches back and forth, causing double writing in the cluster. When there is data replication delay, applications may experience data corruption or data conflict issues. An effective architecture to avoid single points of failure is to use shared storage, and single point failure switching can be monitored by a distributed sentinel system.

#### 4\. Failover and Recovery

**Transfer methods and recovery methods**

```
1. Virtual IP or DNS-based service (Keepalived + VIP/DNS, and MMM architecture — MySQL Master-Master Manager).
```

Problem: During the operation of virtual IPs, there can sometimes be an issue where a VIP is bound to multiple servers simultaneously, providing connections. This is also one of the reasons why it's advisable to avoid using the Keepalived+VIP and MMM architecture, as it cannot handle such issues, leading to multi-point writes in the cluster.

```
2. Promote a standby/slave to the primary/master (MHA, QMHA).
```

Attempt to set the original Master to read\_only on to prevent multi-point writes in the cluster. Use the binlog server to retain the Master's Binlog; when data delay occurs, data must be compensated before promoting a Slave to the new Master, otherwise data will be lost.

### Interview Question

#### Database sharding and table splitting

##### How to perform database sharding and table splitting

> **Table Partitioning** by user ID, with each table limited to 3 million data entries.
>
> **Sharding** shard by business scenario and region, with no more than 2000 concurrent connections per shard

**Sharding-jdbc** This client-side solution's**advantages lie in not requiring deployment, low maintenance costs, no need for proxy layer request forwarding, and high performance**, but each system needs to be**coupled** with the Sharding-jdbc dependency, making upgrades troublesome

**Mycat** This proxy-layer solution's**disadvantage is that it requires deployment**, requiring self-maintained middleware, which has high maintenance costs, but the**benefit is that it is transparent to various projects**, and if upgrades are encountered, it can all be handled by the middleware itself

**Horizontal Splitting**: Distribute one table across multiple databases to handle high concurrency and speed up query performance

*   **id** ensures that operations on multiple tables can be performed on the same database when associating business data
*   **range** facilitates scalability and data statistics
*   **hash** allows for more evenly distributed data

**Vertical Splitting**: Splitting one table into multiple tables, some cold data can be split into a redundant database

> Do not prioritize table splitting based on write bottlenecks

*   Data between split databases cannot be queried directly through the database anymore. This will cause deep pagination issues

*   The more databases are split, the higher the likelihood of issues occurring, and the maintenance costs also increase.

*   After splitting databases, it cannot be guaranteed that cross-database transactions will be supported, and other middleware must be used to achieve eventual consistency.

When splitting databases, the first consideration should be to meet the most core scenarios of the business:

1、Order data is split by **User** to improve **User's Full-Process Experience**

2、Super customers causing **Data Skew** can use the finest-grained unique identifier for hash splitting

3、After splitting by the finest-grained level such as order number, the database can no longer perform single-database deduplication

Three questions:

* Rich Query: After adopting split database and split table, how to satisfy cross-database queries? **Use ES** for wide tables

  Leveraging **sharding gateway + sharding business** can achieve **multi-dimensional query capabilities**, but overall performance is poor and it has a certain impact on normal write requests. The most common industry approach to **multi-dimensional real-time queries** is to use **ElasticSearch**

* Data Skew: Data sharding on top of sharding tables

* Distributed Transactions: Modification across multiple databases and write operations between multiple microservices leading to distributed transaction issues?

* Deep Pagination Issues: Cursor-based queries, or carrying the maximum ID from the previously sorted query in each new query

#### How to migrate old data

**Double-write without interrupting migration**

*   All write operations in the online system, such as add, delete, and modify, **except for add, delete, and modify on the old database, also add them to the new database**
*   After the system is deployed, you still need to run a program to read data from the old database and write to the new database. When writing, you need to check the updateTime
*   Loop until the data in both libraries is completely consistent, then just redeploy the code for sharding tables

#### System performance evaluation and scaling

Jiajiaqin currently has 100 million users: scenario 100,000 write concurrency, 1 million read concurrency, 6 billion data volume

Design considers extreme cases, 32 libraries \* 32 tables ~ 64 tables, a total of 1,000 ~ 2,000 tables

*   Supports **30,000** write concurrency, paired with MQ to achieve a writing speed of 100,000 per second

*   Read-write separation **60,000** read concurrency, paired with distributed caching for 100 read concurrency per second

*   2,000 tables with each containing 3 million records, capable of handling up to 6 billion data entries

*   32 user tables supporting hundreds of millions of users, with room for at most one expansion in the future

**Steps for dynamic expansion**

1.  It is recommended to use 32 stores \* 32 tables, which might be enough for our company for several years.
2.  Configure the routing rules, where uid % 32 = store, uid / 32 % 32 = table
3.  During expansion, apply for more database servers to be added, expanding exponentially
4.  The DBA is responsible for migrating the libraries from the original database server to the new database server.
5.  Modify the configuration, republish the system, go live, and the original routing rules don't need to be changed.
6.  Directly continue to provide services for the online system based on n times the resources of the database server.

#### How to generate an auto-incrementing ID primary key

*   Using Redis can
*   A low concurrency can start a separate **service** to generate auto-increment ID
*   Setting the database **step** auto-increment step can support horizontal scalability
*   UUID is suitable for filenames, numbering, but **not suitable as a primary key**
*   **snowflake snowflake algorithm**, integrates **41 time** (ms), **10 machine**, **12 sequence number** (auto-increment within ms)

Among them, the reserved 10 bits for the machine can be configured according to your business scenario

### Online failures and optimizations

#### Update failure | Master-slave synchronization delay

We have indeed handled bugs in production caused by master-slave synchronization delays in the past, which were minor production incidents.

This is the scenario. A colleague wrote the code logic like this. First, insert a piece of data, then retrieve it, and then update that data. During peak production hours, write operations reached 2000/s, and at that time, the master-slave replication delay was around a few dozen milliseconds. Online, we noticed that there were always some data that we expected to update the status of important data, but failed to update during peak hours. Users reported to customer service, and the customer service then reported to us.

We used the MySQL command:

```
show slave status
```

Check `Seconds_Behind_Master` to see how many milliseconds the replica is behind the master in copying data.

Generally speaking, if the master-slave delay is severe, there are the following solutions:

*   Split the database, divide it into multiple master databases, and the write concurrency of each master database is reduced several times, making the master-slave delay negligible.
*   Rewrite the code, the code writers need to be cautious, when inserting data, it might not be immediately queryable.
*   If it is indeed necessary to insert first, immediately request to query, and then immediately perform some reverse operations on this query, either by **directly connecting to the master database** or by **delaying the query**. The master-slave replication delay is generally not more than 50ms.

#### **Application crash | Database sharding and table partitioning optimization**

We have an online access record table, and due to the large amount of data, it underwent database sharding and table partitioning. During the initial phase of sharding and partitioning, some issues frequently occurred. A typical example is the use of deep pagination in access record queries. Using tools like MAT and Jstack to trace these issues revealed that they were caused by internal references within sharding-jdbc.

Traffic record data is stored in two databases. If no **sharding key** is provided, the query statement will be distributed to all databases. For example, if the query statement is limit 10, offset 1000, the final result only needs to return 10 records, but the database middleware needs to complete this calculation by (1000+10)\*2=2020 records. If the offset value is too large, memory usage will surge. Although sharding-jdbc uses a merging algorithm for some optimizations, deep pagination still causes **memory and performance** issues in practical scenarios.

This **merging and aggregation** operation on intermediate nodes is very common in distributed frameworks. For example, in ElasticSearch, there is a similar data retrieval logic, and **unrestricted deep pagination** can also cause memory issues in ES.

**Industry Solutions:**

**Method 1: Global Vision Method**

（1）Change "order by time offset X limit Y" to "order by time offset 0 limit X+Y"

(2) The service layer sorts the N\*(X+Y) records obtained in memory, and then takes Y records after the offset X after the in-memory sort

This method degrades in performance as the page turns.

**Method 2: Business Compromise Method - Prohibit Page Skip Query**

(1) Use the normal method to obtain the first page data and get the time\_max of the first page records

（2）Each time you change pages, change "order by time offset X limit Y" to "order by time where time>$time\_max limit Y"

To ensure only one page of data is returned each time, with constant performance.

**Method 3: Business Compromise Method - Allow Fuzzy Data**

（1）Change "order by time offset X limit Y" to "order by time offset X/N limit Y/N"

**Method 4: Secondary Query Method**

（1）Change "order by time offset X limit Y" to "order by time offset X/N limit Y"

（2）Find the minimum value time\_min

（3）between secondary query, order by time between $time_min and $ time\_i\_max

（4）Set a virtual time\_min, find the offset of time\_min in each sub-database, thereby obtaining the global offset of time\_min

（5）Got the global offset of time\_min, naturally got the global offset X limit Y

#### Query exceptions | SQL optimization

Before sharding and partitioning, there was an SQL statement to query a user by username:

```python
select * from user where name = "xxx" and community="other";
```

To achieve dynamic concatenation, a colleague modified this SQL statement as follows. His intention was that when name or community is passed as empty, the query conditions are dynamically removed. This style is very common in MyBatis configuration files. Most of the time, this style is fine because the result set can be controlled. However, as the system runs, the number of records in the user table increases. When both name and community passed are empty, a tragic thing happened:

```
select * from user where 1=1
```

All records in the database will be queried and loaded into the JVM's memory. Since there are too many database records, the memory is directly overwhelmed. Memory overflow caused by this reason occurs very frequently, such as when importing Excel files.

The usual solution is to **force pagination functionality** or to **validate some required parameters**

**Controller layer**

Now many projects adopt a front-end and back-end separation architecture, so methods in the Controller layer generally use the @ResponseBody annotation to parse the query results into JSON data and return them. In cases of very large datasets, this will consume a lot of memory resources. If the result set occupies 10MB of memory before being parsed into JSON, it may use 20M or more during the parsing process.

Therefore, it is very necessary to keep the result set concise, which is also the reason for the existence of DTO (Data Transfer Object). The internet environment is not afraid of high concurrent requests for small result sets, but it is very afraid of time-consuming requests for large result sets, which is one of the reasons.

**Service layer**

The Service layer is used to handle specific business logic, more aligned with the functional requirements of the business. A Service may be used by multiple Controller layers, and it may also use query results from multiple DAO structures for calculation and assembly.

```java
int getUserSize() {
                List<User> users = dao.getAllUser();
        return null == users ? 0 : users.size();
}
```

A time bomb was discovered during code review, which only reveals problems when the data volume reaches a certain level.

**ORM Layer**

For example, when using Mybatis, there is a batch import service. When MyBatis executes batch insertion, it produced an out-of-memory error. According to theory, such insertion operations should not cause additional memory usage. The issue was finally traced through the source code.

This is because MyBatis processes the batch in a loop, with the operation object being an array. When we define the interface, we use a List. When a very large List is passed in, it needs to call the List's toArray method to convert the list to an array (shallow copy). In the final assembly stage, StringBuilder is used to concatenate the final SQL, so the actual memory used is much more than the List.

It has been proven that whether it is insertion operations or query actions, as long as the dataset involved is very large, problems are prone to occur. Due to the introduction of many frameworks in the project, it becomes very difficult to analyze the specific memory usage. Therefore, maintaining small batch operations and keeping the result sets clean is a very good habit.