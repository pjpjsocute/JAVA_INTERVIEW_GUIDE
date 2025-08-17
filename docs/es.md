---
title: Elasticsearch
layout: default
has_toc: true            # 保险起见，也在页面里显式打开
toc_levels: [2,3,4,5]
---


# Elasticsearch

> Elasticsearch enables **sub-second** search. A cluster is a distributed deployment, **easily scalable**, which makes it capable of handling petabyte-level data capacity. Most importantly, Elasticsearch can sort search results by score, providing us with the most **relevant** search outcomes.

- TOC
{:toc}
## 1. Overview

### Features

1. **Easy to install**: No other dependencies; after downloading, installation is straightforward. With just a few parameter changes, a cluster can be set up.
2. **JSON**: Input/Output format is JSON, meaning no schema definition is required — quick and convenient.
3. **RESTful**: Almost all operations (indexing, querying, even configuration) can be performed via HTTP APIs.
4. **Distributed**: Nodes are equal to each other (any node can be used as an entry point), and load balancing is automatic when adding nodes.
5. **Multi-tenancy**: Different use cases can be isolated by creating different indexes, and multiple indexes can be queried simultaneously.
6. **Support for massive data**: Can scale to PB-level structured and unstructured data, with near real-time processing of large volumes.

### Functions

- **Distributed search engine**  
  Automatically distributes large datasets across multiple servers for storage and retrieval.

- **Full-text search**  
  Provides fuzzy search and other flexible query options, with relevance ranking, highlighting, etc.

- **Data analytics engine (aggregation)**  
  For example, user logins over the past week or monthly feature usage statistics.

- **Near real-time processing of massive data**  
  Thanks to its distributed architecture, Elasticsearch can leverage multiple servers for storage and queries.

### Scenarios

- **Search-related use cases**  
  e.g., employee search, device search, in-app search, order search.

- **Log analysis**  
  Classic **ELK stack** (Elasticsearch/Logstash/Kibana) for log collection, storage, and analysis.

- **Alerting platforms & analytics**  
  e.g., community group-buying alerts when a discount drops below a threshold, triggering a notification.

- **Business Intelligence (BI)**  
  Analyzing user spending in a specific area and product categories, generating reports, predicting bestsellers, and creating targeted recommendations. Elasticsearch handles analysis/mining; Kibana provides visualization.

### Competitor Analysis

**Lucene**  
A Java-based search toolkit (jar library). It’s only a framework, and mastering it is complex.

**Solr**  
Built on **Lucene**, exposes an HTTP interface. A search engine system with many Lucene details abstracted away.

**Elasticsearch**  
Distributed, near real-time search engine built on **Lucene**. Indexes every field so it can be searched.

### Comparison

1. Solr uses **Zookeeper** for distributed management, while Elasticsearch has its own coordination features.
2. Solr offers more comprehensive functionality, whereas Elasticsearch focuses on core features; advanced features are often provided by third-party plugins.
3. Solr is stronger in traditional search applications, while Elasticsearch excels at real-time search.

The mainstream version is still **Elasticsearch 7.x**, with 7.8 being the latest.

- Optimizations: **Bundled JDK**, upgraded Lucene 8 with major **TopK performance improvements**, and circuit breakers to **prevent OOM**.

---

## 2. Basic Concepts

### IK Analyzer

IKAnalyzer is an open-source, Java-based lightweight Chinese text segmentation tool. Version 3.0 became a standalone Java library, with built-in optimizations for Lucene.

Key features:
1. **Forward iteration fine-grained algorithm**, capable of **600,000 characters/sec** processing.
2. **Multi-sub-processor mode**: supports English letters (IP, Email, URL), numbers (dates, quantities, Roman numerals, scientific notation), Chinese terms (names, places), etc.
3. **Optimized dictionary storage** for smaller memory footprint.
4. Query parser (IKQueryParser) optimized for Lucene full-text search.
5. Combinatorial expansion for higher recall.

- **Custom dictionary**: ext_dict
- **Stopword dictionary**: stop_dict
- **Synonym dictionary**: same_dict

### Index (like a database)
- `settings`: defines shard/replica count.

### Mapping (like table schema)
- Field data types.
- Analyzer type.
- Whether stored/indexed.

### Document (data)
- Full update via `PUT`.
- Partial update via `POST`.

---

## 3. Advanced Features

### Advanced Mapping

- **Geo-point type**  
  Stores latitude/longitude; allows distance calculation or “point-in-area” checks. Must explicitly set type to `geo_point`.

- **Dynamic mapping**  
  Automatically infers field types and adds them to the mapping.

### Advanced DSL

- **Match all query**
- **Full-text queries**
    - `match`
    - `match_phrase`
    - `query_string`
    - `multi_match`
- **Term-level queries**
    - `term`
    - `ids`
    - `range`
    - `prefix`
    - `wildcard`
    - `regexp`
    - `fuzzy`

Also includes: compound queries, sorting, pagination, highlighting, bulk operations.

### Aggregations

- **Metric aggregations**: max, min, sum, avg, etc.
- **Bucket aggregations**: `group by`-like bucketing, with metrics applied within buckets.

### Smart Suggestions

- **Term Suggester**
- **Phrase Suggester**
- **Completion Suggester**
- **Context Suggester**

Precision order: Completion > Phrase > Term  
Recall order: Term > Phrase > Completion

Performance: Completion is fastest. Use Phrase/Term carefully to avoid high resource usage.

---

## 4. Practical Usage

### Write Optimization

- Set **replicas = 0** during bulk initialization; restore later.
- Use **auto-generated IDs** to avoid existence checks.
- Choose analyzers wisely (e.g., `binary` vs `text`).
- Disable scoring and extend refresh intervals.
- Use **batch operations** (`bulk`).

### Read Optimization

- Use **filters** instead of queries when scoring is unnecessary.
- Group data by day/month/year to limit query scope to smaller indexes.

### Zero-Downtime Reindexing

1. **External data import via MQ**  
   Trigger ES reindex through MQ message consumption.

2. **Scroll + Bulk + Aliases**  
   Create a new index, scroll source data in batches, bulk insert into new index, then atomically switch alias.

3. **Reindex API**  
   Available in ES v6.3.1+, wraps scroll/bulk internally.

**Participation & flexibility**: custom > scroll+bulk > reindex  
**Stability & reliability**: custom < scroll+bulk < reindex

---

### Deep Paging Performance Solutions

Deep paging (e.g., using `from + size` for very large offsets) is extremely inefficient because ES must scan and discard all skipped documents. For example, sending a broadcast to all users in a province cannot rely on naïve `from+size` queries.

**Solutions**:
1. **Search After**  
   Use the `search_after` parameter with a sort key (like a unique timestamp or ID). This avoids recalculating large offsets and is more efficient for scrolling through large datasets.

2. **Scroll API**  
   Designed for extracting large numbers of results in batches. Maintains a snapshot of data, efficient for exporting or processing all results.

3. **Index Partitioning**  
   Split data by time (daily/monthly indexes) or region to narrow query scope and reduce result size.

4. **Use Aggregations Instead of Pagination**  
   In many cases, what you really need is summarized/aggregated results, not deep-paged individual documents. Aggregations can avoid deep pagination entirely.

👉 **Best practice**: For user-facing queries, use `search_after` with proper sorting. For backend/batch jobs, use the `scroll` API. Always design indexes with partitioning strategies to minimize the need for deep pagination.  