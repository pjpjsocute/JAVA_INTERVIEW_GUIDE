---
title: Distributed System
layout: default
has_toc: true            # 保险起见，也在页面里显式打开
toc_levels: [2,3,4,5]
---



# **Distributed Computing**

- TOC
{:toc}

> A distributed system is a system in which hardware or software components are distributed across different networked computers, communicating and coordinating with each other solely through message passing.

### **Development history**

*   Entry-level Load Balancing

    *   Gateway Load Balancing
    *   Client Load Balancing
*   Monolithic architecture

    *   Separation of application services and data services
    *   Application service clustering
    *   Centralization of application services as SaaS
*   Database master-slave read-write separation

    *   Full-text search engine accelerates data statistics
    *   Cache cluster alleviates database read pressure
    *   Distributed message middleware alleviates database write pressure
    *   Database horizontal sharding adapts to microservices
    *   Database vertical sharding solves slow queries
*   Context partitioning splits microservices

    *   Service registration and discovery (Eureka, Nacos)
    *   Dynamic update configuration (Config, Apollo)
    *   Business gray release (Gateway, Feign)
    *   Unified security authentication (Gateway, Auth)
    *   Service degradation and rate limiting (Hystrix, Sentinel)
    *   Interface inspection monitoring (Actuator, Prometheus)
    *   Full service chain tracing (Sleuth, Zipkin)

### CAP

*   **Consistency** (2PC, 3PC, Paxos, Raft)
    *   Strong consistency: **Database consistency**, sacrificing performance
        *   **ACID**: Atomicity, Consistency, Isolation, Durability
    *   Weak consistency: **Database and cache**, **delayed double deletion, retry**
    *   Monotonic read consistency: **Cache consistency**, ID or IP hash
    *   Eventual consistency: **Edge business**, message queue
*   **Availability** (multi-level cache, read-write separation)
    *   **BASE** Basically Available: Rate limiting causes slow response speed, degradation leads to poor user experience
        *   Basically Available
        *   Soft state
        *   Eventual Consistency
*   Partition tolerance (Consistent Hashing solves scaling issues)

### Consistency

#### XA Scheme

**2PC** Protocol: Two-phase commit protocol, P refers to the **Prepare** phase, C refers to the **Commit** phase

*   Prepare Phase: Inquire if it can start, write Undo and Redo logs, receive response
*   Commit Phase: Perform Redo logs for **Commit**, perform Undo logs for **Rollback**

**3PC** Protocol: Divides the commit phase into three stages: **CanCommit**, **PreCommit**, and **DoCommit**

**CanCommit**: Sends a canCommit request and starts waiting

**PreCommit**: Receives all Yes, writes Undo and Redo logs. Timeout or No, then abort

**DoCommit**: Executes the Redo log to perform **Commit**, executes the Undo log to perform **Rollback**

The difference is in the second step, participants **increased their own timeouts**, allowing **resources to be released promptly upon failure**

#### **Paxos Algorithm**

> How to quickly and correctly reach consensus on the value of a certain data within a cluster in a distributed system that encounters exceptions

The consistency of participants (e.g., Kafka) can be ensured by coordinators (e.g., Zookeeper), but **the consistency of coordinators can only be guaranteed by Paxos**

Roles in the Paxos algorithm:

*   **Client**: Client, for example, a write request for a file in a distributed file server.
*   **Proposer**: Proposal initiator, selects the maximum N corresponding to V based on Accept returns, and sends \[N+1, V\]
*   **Acceptor**: Decision-maker, after accepting, will reject proposals less than N and return its \[N, V\] to the Proposer
*   **Learners**: Learners as the ultimate decision learners, learners act as the replication factor for this protocol

```java
// Algorithm constraints

P1: An Acceptor must accept the first proposal it receives.
     //Considering that a majority is required, an Acceptor may accept multiple proposals with the same value v.


P2a: If a proposal with value v has been accepted,then any proposal with a higher proposal number that is accepted by an Acceptor must also have value v.

P2b: If a proposal with value v has been accepted, then any proposal with a higher proposal number that is issued by a Proposer must also have value v.


//How to ensure that once a proposal with value v is chosen by Acceptors, all future proposals issued by Proposers will have a higher proposal number and still carry value v?

For any [Mid, Vid], there exists a majority set S of Acceptors such that one of the following holds:
- All proposals accepted by S have numbers greater than Mid
- If any proposal accepted by S has a number less than Mid, then the one with the largest number among them must have value Vid
```

![image-20210112225118095](https://tva1.sinaimg.cn/large/008eGmZEly1gmlato63bnj319m0u0wmi.jpg)

Interview Question: How to Ensure the Liveness of the Paxos Algorithm

Suppose there is an extreme scenario where two Proposers sequentially propose a series of proposals with incrementing numbers, leading to a dead loop where no value is eventually selected.

*   By selecting the main Proposer, it is stipulated that only the main Proposer can propose proposals. As long as the main Proposer and more than half of the Acceptors can communicate normally over the network, the main Proposer proposes a proposal with a higher number, which will ultimately be approved.
*   The time at which each Proposer sends a submitted proposal is set to be **random within a period**, ensuring that it will not result in an infinite loop

#### **ZAB algorithm**

#### Raft algorithm

> Raft is an algorithm for managing the consistency of replicated logs

Raft uses a **heartbeat mechanism** to trigger elections. When a server starts, the initial state of all servers is **follower**. Each server has a timer with a timeout duration of election timeout (**typically 150-300ms**). If a server **does not receive** any messages from the leader or candidate within the timeout period, the **timer is reset**. If the timeout occurs, it **initiates an election**.

**Leader failure**: During the failure, followers will timeout and initiate an election. After completing the election, the new leader compares the progress (term/length) with each other.

**Follower Exception:** After recovery, it synchronizes directly to the current state of the Leader.

**Multiple Candidates:** During the election, if it fails, it continues the election after a timeout.

#### Consistency between the database and Redis

**Full cache ensures efficient reading**

All data is stored in the cache. Read services no longer degrade to the database when querying, as all requests fully rely on the cache. At this point, the spike issues caused by degrading to the database are resolved. However, full caching **has not solved the distributed transaction** problem during updates; instead, it has exacerbated the issue. Full caching **places stricter requirements on data updates**, requiring that all existing data and real-time updated data from the database must be fully synchronized to the cache without any omissions. To address this problem, an effective solution is to adopt **subscribing to the database's Binlog** for data synchronization.

Currently, many open-source tools (such as **Alibaba's Canal** and others) can simulate the master-slave replication protocol. By simulating the protocol to read the master database's Binlog files, they obtain all changes made to the master database. For these changes, they provide various interfaces for business services to retrieve data.

Mount the Binlog middleware to the target database, and you can **real-time obtain all change data** of this database. After parsing these change data, you can **directly write them into the cache**. The advantages also include:

* Greatly improved read speed and reduced latency

* Binlog's master-slave replication is based on the **ACK** mechanism, solving the problem of distributed transactions

  If the cache synchronization fails, the consumed Binlog will not be confirmed, and it will be consumed again next time, eventually writing the data to the cache

**Disadvantages** are inevitable: 1. Increased complexity 2. Consumes cache resources 3. Requires data filtering and compression 4. Potential data loss in extreme cases



Can be supplemented through asynchronous calibration schemes, but it will degrade database performance. However, this scheme will hide the details of middleware usage errors. In the early stages of the online environment, it is more important to log and troubleshoot before making subsequent optimizations, and not to invert the priorities.

### Availability

#### **Heartbeat detection**

> Report the current node status to other nodes at **fixed frequencies**. Receiving a heartbeat indicates that the network and node status are healthy. When reporting heartbeats, additional **status and metadata are usually included for management**

**Periodic heartbeat detection mechanism**: Timeout not returned

**Accumulative failure detection mechanism**: Retry exceeds count

#### **Real-time hot standby in multiple data centers**



Two sets of cache clusters can be deployed in data centers in different cities. The read service is also deployed in different cities or partitions accordingly. When handling requests, read services in different data centers or partitions only rely on cache clusters with the same attributes. This solution has two advantages.

1.  **Improved performance.** Read services should not be layered; read services should be as close to the cache data source as possible.
2.  **Increased availability.** When a single data center fails, all traffic can be switched to the surviving data center or partition within seconds.

While this solution improves performance and availability, the cost is an increase in resource costs.

### Partition fault tolerance

> The ability of distributed systems to tolerate errors

Enhancing system robustness through methods such as rate limiting, degradation, fallback, retry, and load balancing

#### Log replication

![img.png](../pic/pc.png)<

1.  **Leader** adds the command to the log, initiates RPC to other servers to have them replicate this information
2.  **Leader** will continuously retry until all Followers have responded with ACK and replicated all log entries
3.  Notify all **Follower**s to submit, while the Leader should check the status of this log and return it to the client

#### **Master-Slave**

When the master fails, the slave takes over all the master's work. After the master recovers, the service is switched back to the master in an automatic (**hot standby**) or manual (**cold standby**) manner. This is commonly used in **Mysql** and **Redis**.

The foundation of data replication between MySQL is the **binary log file** (binary log file). All operations in the database are recorded in the binary log as **"events"**. Other databases act as slaves by maintaining communication with the master server through an **I/O thread** and **monitoring** changes in the master's binary log file. If a change is detected in the master's binary log file, it is replicated to the slave's **relay log**. Then, a SQL thread on the slave executes the relevant **"events"** into its own database, achieving consistency between the slave and master databases, thus implementing **master-slave replication**.

#### **Active-Active**

Refers to two hosts **running** their respective services simultaneously and **monitoring** each other's status. In the database high availability section, a common active-active setup is the **MM** mode. The MM mode, which stands for **Multi-Master** mode, means a system has multiple masters, each with **read-write** capabilities, and they merge versions based on **timestamps** or **business logic**.

#### **Cluster mode**

Refers to multiple nodes running simultaneously, with the ability to **share** service requests through a master node. For example, Zookeeper. A cluster mode needs to address the high availability of the master node itself, typically using a master-standby mode.

### Distributed transactions

#### XA Scheme

**Two-Phase Commit** | **Three-Phase Commit**

*   Resource locking during the preparation phase has performance issues, and in severe cases, it can cause deadlocks.
*   After submitting a transaction request, a network anomaly occurs, with some data received and executed, leading to consistency issues.

#### TCC solution

**Try Confirm Cancel / Short transaction**

*   **Try** Phase: This phase refers to the detection of resources for various services and the **locking or reservation** of resources.

*   **Confirm** Phase: This phase refers to the **execution of actual operations** in various services.

*   **Cancel** Phase: If any service's business method execution fails, it is necessary to **compensate** or **rollback**.

#### **Saga Scheme**

Transactional Compensation / Long Transactions

*   Process **long**, process **multiple**, call third-party business

#### **Local Message Table (eBay)**

#### **Eventual Consistency in MQ**

For example, Alibaba's RocketMQ supports message transactions (core: **two-end confirmation, retry idempotence**)

1.  The A**(order)** system first sends a **prepared** message to mq. If the prepared message fails to send, the operation is canceled
2.  After successful transmission, execute the local transaction. If it succeeds or fails, send **confirmation and rollback** messages to mq
3.  If confirmation messages are sent, then at this point, the B**(Inventory)** system will receive the confirmation message and then execute its local transaction
4.  mq will automatically **periodically poll** all prepared message callback interfaces to confirm the transaction execution status
5.  B's affairs fail automatically**keep retrying**until successful, after reaching a certain number of times, send an alarm for manual**manual rollback**and**compensation**

#### Maximum Effort Notification Plan (Order -> Points)

1.  After the local transaction in System A is completed, it sends a message to the MQ;
2.  There will be a dedicated service for consuming MQ**best-effort notification service**, then calling the interface of system B;
3.  If system B execution fails, try to re-call system B at regular intervals, **repeated N times**, and still fail **give up**

You can find a scenario with strict **financial** requirements where you can say you are using the **TCC solution**;

If it is a general distributed transaction scenario, such as **point** data, you can use a reliable message **eventual consistency solution**

If the distributed scenario **allows inconsistency**, you can use the best-effort notification solution

### Interview Question

#### Distributed Session implementation scheme

*   Based on JWT Token, data is obtained from cache or database
*   Redis based on Tomcat, simple configuration of conf file
*   Spring-based Redis, supports SpringCloud and Springb