---
title: Vocabulary
layout: default
has_toc: true            # 保险起见，也在页面里显式打开
toc_levels: [2,3,4,5]
---

# 中英文词汇表对照

- TOC 
{:toc}

### 计算机网络

| 中文       | 英文                                                         |      |
| ---------- | ------------------------------------------------------------ | ---- |
| 幂等性     | Idempotence                                                  |      |
| 无状态     | Stateless                                                    |      |
| 非对称加密 | Asymmetric encryption                                        |      |
| 瓶颈       | <font style="color:rgb(36, 36, 36);background-color:rgb(255, 250, 246);">Bottlenecks</font> |      |
| 指数       | <font style="color:rgb(36, 36, 36);background-color:rgb(255, 250, 246);">Exponential</font> |      |
| 弹性       | <font style="color:rgb(36, 36, 36);background-color:rgb(255, 250, 246);">Resilience</font> |      |
| 抖动       | <font style="color:#0e0e0e;">Jitter</font>                   |      |
| 分区容错   | <font style="color:rgb(31, 35, 40);">Partition Tolerance</font> |      |
| 流量洪峰   | <font style="color:#0e0e0e;">Traffic spikes</font>           |      |


### 操作系统

| 中文   | 英文                                       |      |
| ------ | ------------------------------------------ | ---- |
| 时间片 | Time slice                                 |      |
| 内核   | Kernel|      |


### Java

| 中文                                                         | 英文                                                         |                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |--------------------------|
| 垃圾回收器                                                   | garbage collecter                                            |                          |
| 动态代理                                                     | dynamic proxy                                                |                          |
| 多态                                                         | <font style="color:rgb(13, 13, 13);">Polymorphism</font>     |                          |
| 封装                                                         | <font style="color:rgb(13, 13, 13);">Encapsulation</font>    |                          |
| 继承                                                         | **<font style="color:rgb(0, 29, 53);">Inheritance</font>**   |                          |
| 平行                                                         | <font style="color:rgb(52, 152, 194);">horizontal</font>     | 一般是水平扩容，horizontal scale |
| 垂直                                                         | <font style="color:rgb(52, 152, 194);">vertical</font><font style="color:rgb(11, 65, 100);background-color:rgb(41, 44, 50);"></font> |                          |
| 信号量                                                       | <font style="color:rgb(52, 152, 194);">semaphore</font>      |                          |
| 可重入锁                                                     | <font style="color:rgb(52, 152, 194);">ReentrantLock</font>  |                          |
| 自旋锁                                                       | <font style="color:rgb(52, 152, 194);">spin lock</font>      |                          |
| 尺度                                                         | <font style="color:rgb(52, 152, 194);">Scale</font>          |                          |
| **<font style="color:rgb(52, 152, 194);">泛型</font>****<font style="color:#0e0e0e;"></font>** | <font style="color:#0e0e0e;">Generics</font>                 |                          |
| **<font style="color:rgb(52, 152, 194);">ACID</font>**       | <br/>+ **<font style="color:rgb(31, 35, 40);">Atomicity</font>**<font style="color:rgb(31, 35, 40);"> </font><font style="color:rgb(31, 35, 40);">- Each transaction is all or nothing</font><br/>+ **<font style="color:rgb(31, 35, 40);">Consistency</font>**<font style="color:rgb(31, 35, 40);"> </font><font style="color:rgb(31, 35, 40);">- Any transaction will bring the database from one valid state to another</font><br/>+ **<font style="color:rgb(31, 35, 40);">Isolation</font>**<font style="color:rgb(31, 35, 40);"> </font><font style="color:rgb(31, 35, 40);">- Executing transactions concurrently has the same results as if the transactions were executed serially</font><br/>+ **<font style="color:rgb(31, 35, 40);">Durability</font>**<font style="color:rgb(31, 35, 40);"> - Once a transaction has been committed, it will remain so</font> |                          |
| <font style="color:#0e0e0e;">单一服务</font>                 | <font style="color:rgb(36, 36, 36);background-color:rgb(255, 250, 246);">monolithic</font> |                          |
| 元空间                                                       | Metaspace                                                    |                          |
| 年轻代 / 老年代                                              | Young / Old Generation                                       |                          |


### Spring

| 中文       | 英文                                                         |      |
| ---------- | ------------------------------------------------------------ | ---- |
| 控制翻转   | <font style="color:rgb(17, 17, 17);">Inversion of Control</font> |      |
| prometheus | prometheus(pro mi ti ers)                                    |      |


### 设计原理

| 中文                                                   | 英文                                                         |                                                              |
| ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(13, 13, 13);">防腐层</font>     | <font style="color:rgb(13, 13, 13);">Anti-Corruption Layer</font> |                                                              |
| <font style="color:rgb(13, 13, 13);">界限上下文</font> | <font style="color:rgb(13, 13, 13);">Bounded Context</font>  |                                                              |
| 充血模型                                               | <font style="color:rgb(13, 13, 13);">Rich Model</font>       |                                                              |
| 贫血模型                                               | <font style="color:rgb(13, 13, 13);">Anemic Model</font>     |                                                              |
| 聚合                                                   | <font style="color:rgb(13, 13, 13);">Aggregate</font>        |                                                              |
| 高内聚                                                 | <font style="color:rgb(13, 13, 13);">High Cohesion</font>    |                                                              |
| 低耦合                                                 | <font style="color:rgb(13, 13, 13);">Low Coupling</font>     |                                                              |
| 中台                                                   | <font style="color:rgb(25, 27, 31);">Middle Platform</font>  | **<font style="color:rgb(25, 27, 31);">Shared Services Platforms</font>** |
| 架构演进                                               | <font style="color:#000000;">Architecture Evolution</font>   | <font style="color:rgb(13, 13, 13);">Software architecture evolves.</font> |
| 粗算                                                   | back-of-envelope                                             |                                                              |
| google的聚类算法                                       | <font style="color:rgb(31, 35, 40);">MapReduce</font>        |                                                              |
| 权衡取舍                                               | Trade-Offs                                                   |                                                              |
| 副本                                                   | <font style="color:rgb(31, 35, 40);">Replicas</font>         |                                                              |
| 主从                                                   | Master-Slave                                                 |                                                              |
| 不均衡                                                 | inevenly                                                     |                                                              |
| 原子的                                                 | <font style="color:rgb(71, 71, 71);">Atomicity</font>        |                                                              |
| 调优                                                   | <font style="color:rgb(31, 35, 40);">tuning</font>           |                                                              |
| 基线                                                   | <font style="color:rgb(31, 35, 40);">Benchmark</font>        |                                                              |
| 反向代理                                               | <font style="color:#000000;">Reverse proxies</font>          |                                                              |
| 熔断                                                   | Circuit Breaker                                              |                                                              |
| 限流                                                   | Rate Limiting                                                |                                                              |
| 降级                                                   | Graceful Degradation                                         |                                                              |
| 灰度发布                                               | Canary Release                                               |                                                              |
| 滚动更新                                               | Rolling Update                                               |                                                              |
| 蓝绿部署                                               | Blue-Green Deployment                                        |                                                              |
| 多租户                                                 | Multi-tenancy                                                |                                                              |
| 吞吐量                                                 | Throughput                                                   |                                                              |
| 容错性                                                 | Fault Tolerance                                              |                                                              |
| 可扩展性                                               | Scalability                                                  |                                                              |
| 健壮性                                                 | Robustness                                                   |                                                              |


### 数据库

| 中文                                           | 英文                                                        |                                              |
| ---------------------------------------------- | ----------------------------------------------------------- | -------------------------------------------- |
| <font style="color:#0e0e0e;">反范式设计</font> | <font style="color:#0e0e0e;">Denormalized Design</font>     | <font style="color:rgb(13, 13, 13);"></font> |
| 数据库架构                                     | <font style="color:rgb(31, 35, 40);">Database schema</font> |                                              |
| 联合索引                                       | Composite Index                                             | <font style="color:rgb(13, 13, 13);"></font> |
| 数据分片                                       | Data Sharding                                               | <font style="color:rgb(13, 13, 13);"></font> |
| 数据一致性                                     | Data Consistency                                            | <font style="color:rgb(13, 13, 13);"></font> |
| 最终一致性                                     | Eventual Consistency                                        |                                              |
| 强一致性                                       | Strong Consistency                                          |                                              |


### 算法

| 中文                                                 | 英文                                                     |      |
| ---------------------------------------------------- | -------------------------------------------------------- | ---- |
| 递归                                                 | Recursion                                                |      |
| <font style="color:rgb(13, 13, 13);">回溯算法</font> | <font style="color:rgb(13, 13, 13);">Backtracking</font> |      |
| <font style="color:rgb(13, 13, 13);">剪枝算法</font> | <font style="color:rgb(13, 13, 13);">Pruning</font>      |      |
| ...几次方                                            | power of                                                 |      |
|                                                      |                                                          |      |


### 消息队列

| 中文      | 英文                    |      |
| --------- | ----------------------- | ---- |
| 死信队列  | Dead Letter Queue (DLQ) |      |
| 延迟消息  | Delayed Message         |      |
| 消息积压  | Message Backlog         |      |
| 发布/订阅 | Publish/Subscribe       |      |
| 幂等消费  | Idempotent Consumer     |      |

