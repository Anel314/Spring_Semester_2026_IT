
# IT 2008 — Database Systems: Complete Study Notes

> **Structure:** All topics are summarized as theory. MongoDB, PostgreSQL (pgvector), and Redis include additional code examples and detailed explanations.

---

## Table of Contents

1. [Relational Database Concepts](#1-relational-database-concepts)
2. [SQL — Joins, Set Operators & Aggregations](#2-sql--joins-set-operators--aggregations)
3. [Stored Routines — Triggers, Procedures & Functions](#3-stored-routines--triggers-procedures--functions)
4. [HBase](#4-hbase)
5. [MongoDB](#5-mongodb)
6. [Apache CouchDB](#6-apache-couchdb)
7. [Neo4j](#7-neo4j)
8. [Amazon DynamoDB](#8-amazon-dynamodb)
9. [Redis](#9-redis)
10. [Vector Databases & pgvector](#10-vector-databases--pgvector)
11. [Polyglot Persistence](#11-polyglot-persistence)
12. [Database Genres — Choosing the Right Tool](#12-database-genres--choosing-the-right-tool)

---

## 1. Relational Database Concepts

### Primary Key

A primary key is a column (or set of columns) that **uniquely identifies each row** in a table.

- **Unique** — no two rows can share the same primary key value.
- **Non-null** — every row must have a value.
- **Indexed automatically** — most databases create an index on the primary key by default.
- **Basis for relationships** — primary keys are referenced as foreign keys in other tables.
- Can be a single column or a **composite key** (multiple columns), though single-column, auto-incremented keys are strongly preferred.

**Convention:** Always name the primary key `id` and let the database auto-increment it.

### Foreign Key

A foreign key is a column (or set of columns) in one table that references the primary key in another table, establishing a **parent–child relationship**.

- Enforces **referential integrity** — a child row cannot reference a parent row that doesn't exist.
- Supports **cascade actions** on parent modifications:
  - `CASCADE` — propagate changes/deletions to child rows.
  - `RESTRICT` — block changes/deletions if child rows exist.
  - `SET NULL` — set the FK to `NULL` when the parent is deleted.
  - `SET DEFAULT` — set the FK to a default value when the parent is deleted.
- Can represent one-to-one, one-to-many, or many-to-one relationships.

### Naming Conventions

- All names (tables, columns, constraints, indexes) in **lowercase**, words separated by `_`.
- Table names in **plural** (e.g., `students`, `student_courses`).
- Column names in **singular** (e.g., `name`, `date`).
- Primary key always named `id`, managed and auto-incremented by the database.

### Normalization

Normalization is the process of organizing a database to **reduce redundancy and dependency** by decomposing tables into smaller, well-structured ones.

**Why it matters:**
- Improves data integrity.
- Reduces storage space and improves performance.
- Simplifies data maintenance.

**The three normal forms:**

| Form | Rule |
|------|------|
| **1NF** | Every attribute holds only **atomic** (indivisible) values; no repeating groups. |
| **2NF** | All non-key attributes are **fully functionally dependent** on the entire primary key (removes partial dependencies). |
| **3NF** | No **transitive dependencies** — non-key attributes must not depend on other non-key attributes. |

---

## 2. SQL — Joins, Set Operators & Aggregations

### Joins

Joins connect data across one or multiple tables using shared column values (foreign keys). A join is necessary because each table stores only a partial view of the business data.

### Set Operators

| Operator | Behavior |
|----------|----------|
| `UNION` | Merges results of two queries; removes duplicates. `UNION ALL` keeps duplicates and is faster. |
| `INTERSECT` | Returns only rows present in **both** result sets. |
| `EXCEPT` | Returns rows in the first query **not present** in the second. |

All set operators require the same number of columns in the same order with compatible data types.

### Aggregate Functions

Aggregate functions perform a calculation on a set of rows and return a single value (e.g., `SUM`, `AVG`, `COUNT`, `MIN`, `MAX`).

The `GROUP BY` clause groups rows before the aggregation is applied — for example, calculating total payments per customer.

### Subqueries

A subquery is a query nested inside another query. The inner query runs first and its result is used by the outer query. Subqueries can appear in `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statements.

### Window Functions

Window functions perform calculations across a set of rows related to the current row **without collapsing** the result into a single row. Used for running totals, rankings, moving averages, and cumulative sums.

---

## 3. Stored Routines — Triggers, Procedures & Functions

### Stored Routines Overview

A stored routine is a named set of SQL statements saved in the database server. They are used primarily for **data validation** and **access control**. While powerful, they have tradeoffs — increased server load, a learning curve, and potential migration difficulties.

Types: **Functions**, **Procedures**, **Triggers**.

### Triggers

A trigger is a stored program automatically invoked by the DBMS in response to a **DML event** (`INSERT`, `UPDATE`, `DELETE`) on a specific table.

Key concepts:
- **Timing:** `BEFORE` or `AFTER` the DML statement.
- **Scope:** `FOR EACH ROW` — fires once per affected row.
- **`OLD` / `NEW` modifiers:** Access column values before and after the change.
  - `OLD.col` — value before the operation.
  - `NEW.col` — value after the operation.
- Triggers can be enabled/disabled but **cannot be explicitly invoked**.

### Functions vs. Procedures

| Characteristic | Function | Stored Procedure |
|----------------|----------|-----------------|
| Return value | Must return a single value | Can return multiple values, one value, or none |
| Parameters | `IN` only | `IN`, `OUT`, `INOUT` |
| Invocation | Usable in `SELECT`, expressions | Only via `CALL` |
| Determinism | Must declare `DETERMINISTIC` or not | Not required |
| Creation | `CREATE FUNCTION` | `CREATE PROCEDURE` |

**Functions** encapsulate formulas or business rules for reuse inside SQL statements. **Procedures** encapsulate data manipulation logic called explicitly by the application.

---

## 4. HBase

### What Is HBase?

Apache HBase is a **column-oriented, distributed database** built on top of HDFS (Hadoop Distributed File System). It is designed for **massive-scale, online analytics** on datasets of many gigabytes to petabytes. It is based on Google's 2006 Bigtable paper.

Used by Meta, X (Twitter), Airbnb, and others.

### When to Use HBase

- Dataset is in the multi-gigabyte to petabyte range.
- You need to integrate with the Hadoop ecosystem (Hive, Spark, MapReduce).
- You need built-in versioning, strong consistency, and horizontal scalability.

**Do not use HBase** if your cluster has fewer than 5 nodes, or your data is in the megabyte range.

### Key Terminology (Not Relational!)

HBase uses familiar terms with fundamentally different meanings:

| Term | HBase Reality |
|------|--------------|
| Table | A large sorted map — not a relational table. |
| Row | Identified by a row key; acts like a tiny self-contained database. |
| Column | Completely variable; no schema enforcement. |
| Schema | Informs performance (compression), not data structure. |

### Data Model

HBase is a **map of maps**, structured as:
- **Table** — sorted collection of rows by row key.
- **Row** — identified by an arbitrary string row key.
- **Column Family** — logical/physical grouping; must be defined at table creation.
- **Column (Qualifier)** — `family:qualifier`; can be added to any row at any time.
- **Cell** — intersection of row + column; stores multiple timestamped versions.
- **Timestamp** — integer (ms since epoch) enabling built-in versioning.

### Core Strengths

- Horizontal scalability via automatic **region splitting**.
- **Strong consistency** — reads always see the latest write.
- **Row-level atomicity** — all puts on a single row are atomic.
- **Built-in versioning** — historical values stored per cell.
- **Write-Ahead Log (WAL)** — enables crash recovery.
- **Built-in compression** (Gzip by default; LZO recommended by community).
- **Garbage collection** of expired data versions.

### Architecture

- **ZooKeeper** — coordination backbone for the cluster.
- **HDFS** — underlying durable distributed storage.
- **Regions** — contiguous sorted ranges of rows; automatically split when they grow beyond a threshold.
- **WAL (Write-Ahead Log)** — before in-memory data is flushed to disk, writes are logged for recovery.

---

## 5. MongoDB

### What Is MongoDB?

MongoDB is a **document-oriented NoSQL database** that stores data in flexible, JSON-like documents (technically BSON — Binary JSON). It blends the query power of relational databases with the distributed nature of NoSQL systems.

- **Schemaless** — documents within the same collection can have different fields.
- **Scalable by design** — built for horizontal scaling across servers (the name comes from "humongous").
- Used in production at Foursquare, Adobe, CERN, and many others.

### Key Concepts

- **Collection** — analogous to a table; a group of documents.
- **Document** — a JSON-like object with an auto-generated `_id` field.
- **No server-side joins** — related data is typically embedded or denormalized.
- **No predefined schema** — schema changes can be made on the fly.

### When to Use MongoDB

- Web projects needing large-scale data storage on commodity hardware.
- Startups with rapidly evolving data models.
- Applications needing both ad-hoc query power and distributed architecture.
- When your data naturally maps to nested, hierarchical documents.

### CRUD Operations

#### Inserting Documents

Creating a collection is as simple as inserting the first document. If the collection doesn't exist, MongoDB creates it automatically.

```javascript
db.towns.insertOne({
  name: "New York",
  population: 22200000,
  lastCensus: ISODate("2016-07-01"),
  famousFor: ["the MOMA", "food", "Derek Jeter"],
  mayor: {
    name: "Bill de Blasio",
    party: "D"
  }
});
```

#### Finding Documents

```javascript
// Get all documents
db.towns.find();

// Find by _id (must wrap string in ObjectId())
db.towns.find({ _id: ObjectId("59094288afbc9350ada6b807") });

// Projection — include only the 'name' field
db.towns.find({ _id: ObjectId("59094288afbc9350ada6b807") }, { name: 1 });

// Projection — exclude 'name'
db.towns.find({ _id: ObjectId("59094288afbc9350ada6b807") }, { name: 0 });
```

#### Range and Regex Queries

```javascript
// Towns starting with 'P' and population under 10,000
db.towns.find(
  { name: /^P/, population: { $lt: 10000 } },
  { _id: 0, name: 1, population: 1 }
);
```

#### Array Queries

MongoDB handles nested array data natively:

```javascript
// Towns famous for 'food'
db.towns.find({ famousFor: "food" }, { _id: 0, name: 1, famousFor: 1 });

// Towns famous for 'moma' (case-insensitive regex)
db.towns.find({ famousFor: /moma/ }, { _id: 0, name: 1, famousFor: 1 });

// Must be famous for BOTH 'food' and 'beer'
db.towns.find({ famousFor: { $all: ["food", "beer"] } }, { _id: 0, name: 1, famousFor: 1 });

// Famous for NEITHER 'food' nor 'beer'
db.towns.find({ famousFor: { $nin: ["food", "beer"] } }, { _id: 0, name: 1, famousFor: 1 });
```

#### JavaScript Code Queries (Last Resort)

MongoDB can run a custom JavaScript decision function across all documents — but these are slow, cannot be indexed, and will fail if a queried field is missing in even one document. Avoid in production.

```javascript
db.towns.find(function () {
  return this.population > 6000 && this.population < 600000;
});

// Shorthand
db.towns.find("this.population > 6000 && this.population < 600000");
```

### Indexes

Without an index, MongoDB performs a **collection scan** (reads every document). Indexes store a small portion of data in a traversable B-tree structure.

**Types of indexes:**

| Type | Description |
|------|-------------|
| **Single Field** | Index on one field, e.g., `{ score: 1 }` (ascending) or `{ score: -1 }` (descending). The `_id` index is created automatically. |
| **Compound** | Index on multiple fields, e.g., `{ userid: 1, score: -1 }`. Field order matters; can serve queries on prefixes. |
| **Multikey** | Automatically created when indexing an array field; creates one entry per array element. |
| **Text** | Supports full-text search within string fields; one text index per collection. |
| **Geospatial** | `2d` for flat coordinates; `2dsphere` for GeoJSON and spherical geometry. |
| **Hashed** | Indexes the hashed value of a field; used for hashed sharding. |
| **Unique** | Rejects duplicate values (e.g., email addresses). |
| **Sparse** | Only indexes documents where the indexed field exists. |
| **Partial** | Indexes only documents matching a filter expression, reducing index size. |

**Best practices:**
- Only index frequently queried fields.
- Don't over-index — each index has a write overhead.
- Put the most selective field first in compound indexes.
- Use `explain()` to analyze query plans and drop unused indexes.

### Aggregation Pipeline

The aggregation framework processes documents through a **pipeline** of stages. Each stage transforms the documents passed from the previous stage.

**Common pipeline stages:**

| Stage | Purpose |
|-------|---------|
| `$match` | Filter documents (like `WHERE`). |
| `$group` | Group by a field and apply aggregate functions (`$sum`, `$avg`, etc.). |
| `$project` | Reshape documents — include, exclude, or rename fields. |
| `$sort` | Sort documents. |
| `$limit` / `$skip` | Restrict or skip documents. |
| `$unwind` | Deconstruct an array field into individual documents. |
| `$out` | Write results to a new collection. |
| `$merge` | Merge results into an existing collection. |

**Example — count documents per city grouped by status:**
```javascript
db.orders.aggregate([
  { $match: { status: "Shipped" } },
  { $group: { _id: "$city", count: { $sum: 1 } } },
  { $sort: { count: -1 } }
]);
```

### Replication & Sharding

**Replication:**
- **Leader-Follower (Master-Slave):** All writes go to the master; propagated asynchronously to replicas. Straightforward, but master is a single write point.
- **Leader-Leader (Multi-Master):** All nodes accept writes. Higher availability, but requires conflict resolution (e.g., timestamp-based).

**Sharding:** Distributes data across multiple machines (shards). MongoDB uses a **shard key** to determine which shard stores each document, enabling horizontal scaling beyond the capacity of a single server.

---

## 6. Apache CouchDB

### What Is CouchDB?

CouchDB is a **document-oriented database** built on the philosophy of embracing network unreliability. It is engineered to work anywhere — from smartphones to datacenters — even with intermittent connectivity.

Core technology: written in **Erlang** for extreme fault-tolerance.

### Core Characteristics

- **Document-Oriented** — stores flexible JSON documents.
- **REST-based API** — all operations are standard HTTP requests (GET, POST, PUT, DELETE).
- **Append-Only Storage** — data is never overwritten; updates create new revisions, making the database virtually incorruptible.
- **No Ad Hoc Queries** — all data access is through pre-defined, indexed views (no arbitrary SQL-like queries).

### Document Model

Every document has two reserved fields:
- `_id` — unique identifier, immutable.
- `_rev` — revision ID, changes on every update; used for concurrency control and conflict detection.

### Views & MapReduce

Views are the **only** way to query non-trivially. They are built with JavaScript **MapReduce** functions stored inside **design documents** (IDs prefixed with `_design/`).

- **Map function** — emits `(key, value)` pairs from each document.
- **Reduce function** — aggregates values (built-ins: `_sum`, `_count`).
- Views are **incremental** — only changed documents are re-processed, making them highly efficient persistent indexes.

### Multi-Master Replication

- Every CouchDB node is a **master** — all nodes accept reads and writes.
- Replication uses the **Changes API** (`/_changes`) under the hood.
- Can be one-way or bidirectional, ad hoc or continuous, full or filtered.
- Designed for **extreme data durability** in unreliable network environments.

### Conflict Resolution

When two disconnected nodes update the same document version, a conflict occurs. CouchDB:
1. Picks a **deterministic winner** so all nodes agree.
2. Stores the losing revision in a `_conflicts` array.
3. Leaves **merge logic entirely to the application**.

### PouchDB (Offline-First)

PouchDB is a JavaScript database running in the browser that **automatically syncs** with a CouchDB backend when connectivity is restored — enabling offline-first web applications.

### Strengths & Weaknesses

**Strengths:** Extreme robustness, best-in-class multi-master replication, web-native (HTTP/JSON), efficient incremental indexes, scales down to a phone.

**Weaknesses:** No ad hoc queries (all patterns must be known upfront), client-side responsibility for conflict resolution, unsuitable for exploratory data analysis.

---

## 7. Neo4j

### What Is Neo4j?

Neo4j is a **graph database** designed to store and traverse data where **relationships between entities are as important as the entities themselves**. It is described as the "bungee cord" of databases — connecting disparate pieces of information.

### Core Concepts

- **Node** — a data point (vertex) holding key-value properties.
- **Relationship** — a directed, named connection (edge) between two nodes, also holding properties.
- **"Whiteboard Friendly"** — a diagram drawn with boxes and lines maps almost directly to the database model.
- **Schema-flexible** — no rigid schema; missing properties are simply absent.

### Cypher

Cypher is Neo4j's declarative, pattern-based graph query language. It uses ASCII-art-like syntax to describe nodes `()` and relationships `-->`.

### Indexes & Constraints

- **Indexes** speed up node lookups by property without changing Cypher queries.
- **Unique constraints** enforce property uniqueness and automatically create an index.

### Strengths & Weaknesses

| Strengths | Weaknesses |
|-----------|-----------|
| Intuitive modeling of connected data | Cannot natively shard subgraphs across servers |
| Constant-time graph traversals | Enterprise features (HA, backups) require a commercial license |
| Supports billions of nodes/relationships | "Node/relationship" terminology differs from standard graph theory |

### Ideal Use Cases

Social networks, fraud detection, recommendation engines, supply chain and logistics — any domain focused on traversing complex networks of connections.

### When NOT to Use Neo4j

Simple CRUD with minimal relationships, high-volume time-series data, pure document storage, massive analytical aggregations, simple key-value lookups.

---

## 8. Amazon DynamoDB

### What Is DynamoDB?

DynamoDB is a **fully managed NoSQL database service** by AWS. Users do not install, patch, or manage servers — AWS handles infrastructure, replication, and scaling. Described as an "earth mover": powerful, but you must know how to operate it.

### Key Characteristics

- **Massive scale** — handles trillions of requests per day; stores petabytes in a single table.
- **Cloud-native** — deep integration with the AWS ecosystem.
- **Key-Value Plus model** — core key-value store with borrowed relational concepts (tables, secondary indexes).
- **Almost schemaless** — only key attributes are defined at table creation; all other attributes are flexible.

### Primary Keys

The primary key is the **single most important design decision** in DynamoDB:

- **Simple Primary Key (Partition Key only):** Single unique attribute; direct key-value lookup.
- **Composite Primary Key (Partition Key + Sort Key):** Allows multiple items per partition key, physically co-located and sorted by sort key — enables efficient range queries.

### Partitioning

DynamoDB automatically distributes data across partitions using the partition key's hash. For even distribution, choose a partition key with **high cardinality** (many unique values like UUID, User ID). Poor choices create "hotspots" — overloaded partitions causing throttled requests.

### Secondary Indexes

| Type | Scope | Modifiable After Creation? |
|------|-------|---------------------------|
| **LSI (Local Secondary Index)** | Alternative sort key within the same partition key | No |
| **GSI (Global Secondary Index)** | Completely independent key; spans the entire table | Yes |

### Strengths & Weaknesses

**Use DynamoDB when:** You need massive scale with predictable low-latency performance and can define data access patterns in advance.

**Avoid when:** You need complex ad-hoc queries, joins, or relational data models; when access patterns are unknown; or when transactional consistency across multiple items is required.

**Critical warning:** DynamoDB is AWS-locked. Once your architecture depends on it, migrating to another provider is extremely difficult.

---

## 9. Redis

### What Is Redis?

Redis (**RE**mote **DI**ctionary **S**ervice) is a **in-memory data structure server** first released in 2009. Its primary strength is providing server-side access to complex data structures with **extremely high performance**.

Think of Redis as "grease" — it lubricates the moving parts of a system, reducing friction and speeding up overall function.

### Key Characteristics

- **In-Memory** — entire dataset stored in RAM; source of its exceptional speed.
- **Data Structure Server** — natively supports strings, lists, sets, sorted sets, and hashes.
- **Blazing Fast** — benchmarks show 100,000+ SET operations per second.
- **Versatile** — acts as a database, cache, blocking queue, and pub/sub message broker.

### Core CRUD Operations

```
# Set and get a value
SET 7wks http://www.sevenweeks.org/
GET 7wks
# => "http://www.sevenweeks.org/"

# Multi-key set and get
MSET gog http://www.google.com yah http://www.yahoo.com
MGET gog yah
# => 1) "http://www.google.com/"
#    2) "http://www.yahoo.com/"

# Atomic integer increment (perfect for counters)
SET count 2
INCR count     # => 3
INCRBY count 5 # => 8
DECR count     # => 7
```

### Transactions (Atomic Operations)

Redis transactions ensure a sequence of commands executes **atomically** — either all succeed or none do. This is not a SQL ROLLBACK; commands are cleared from the queue before execution, not reverted after.

```
MULTI
SET prag http://pragprog.com
INCR count
EXEC
# => 1) OK
#    2) (integer) 4

# Cancel before executing
DISCARD
```

### Hashes

Hashes model objects as grouped key-value pairs under a single top-level key — much cleaner than key-prefix workarounds. **Important:** Redis hashes cannot be nested; values must be simple strings.

```
# Create a user hash
HMSET user:luc name "Luc" password "s3cret"

# Get a single field
HGET user:luc name
# => "Luc"

# Get all field-value pairs
HGETALL user:luc
```

### Lists

Ordered collections of strings indexed from zero. Can function as **queues** (FIFO) or **stacks** (LIFO).

```
# Push to the right (tail)
RPUSH wishlist http://siteA.com http://siteB.com

# Retrieve range (0 -1 = all elements)
LRANGE wishlist 0 -1

# Pop from the left (FIFO queue)
LPOP wishlist

# Atomic move: remove from tail of one list, push to head of another
RPOPLPUSH wishlist visited
```

**Blocking Lists** — instead of polling, a consumer blocks and waits for an element:

```
# Consumer blocks for up to 300 seconds
BRPOP comments 300

# Producer pushes a comment (unblocks the consumer immediately)
RPUSH comments "New comment text"
```

Related: `BLPOP`, `BRPOPLPUSH`.

### Sets

Unordered collections of **unique strings**. Excel at high-speed mathematical set operations.

```
SADD news http://bbc.com http://cnn.com
SADD tech http://techcrunch.com http://bbc.com

# Intersection (in both sets)
SINTER news tech
# => http://bbc.com

# Difference (in news, not in tech)
SDIFF news tech

# Union (all unique members)
SUNION news tech

# Store results in a new key
SUNIONSTORE all_sites news tech
```

### Key Expiration (Cache Lifecycle Management)

```
# Set TTL on a key
EXPIRE session:abc 3600

# Check remaining TTL (-2 if expired/missing)
TTL session:abc

# Set value AND expiry in one atomic operation
SETEX session:abc 3600 "user_data_here"

# Remove timeout (make permanent)
PERSIST session:abc
```

**Most Recently Used (MRU) pattern:** Update the expiry time on every cache read — frequently accessed data stays hot; stale data is purged automatically.

### Pipelining (Performance Optimization)

Each Redis command normally costs one network round-trip (RTT). **Pipelining** batches multiple commands into a single request, paying the RTT cost only once.

```bash
# Send 3 PINGs in one network round trip
(echo -en "PING\r\nPING\r\nPING\r\n"; sleep 1) | nc localhost 6379
# => +PONG
#    +PONG
#    +PONG
```

Far more efficient than three separate request/response cycles for bulk operations.

### Publish/Subscribe (Pub/Sub)

Pub/Sub is a **one-to-many broadcast** pattern — a publisher sends to a channel, and all subscribers receive it simultaneously. This differs from list-based queues (one-to-one delivery).

```
# Subscriber 1 and 2 subscribe to the 'comments' channel
SUBSCRIBE comments

# Publisher sends a message
PUBLISH comments "New comment from user_42"
# => (integer) 2  (two subscribers received it)

# Unsubscribe from channel
UNSUBSCRIBE comments
```

### Durability & Configuration

Redis prioritizes **speed over durability** by default. Configuration is done in `redis.conf`:

| Setting | Purpose |
|---------|---------|
| `daemonize yes` | Run as a background process (production). |
| `port` | Listening port (default 6379). |
| `logfile` | Path for log output when daemonized. |
| `databases` | Number of logical namespaces (default 16; set to 1 if only one is needed). |

**Two persistence modes:**

- **Snapshotting (`save`):** Asynchronously saves the dataset to disk at configured intervals. Default method. Example: `save 900 1` (save if ≥1 key changed in 900 seconds).
- **Append-Only File (AOF):** Logs every write operation. On restart, replays the log. More durable. Recommended setting: `appendfsync everysec`.

### Master-Slave Replication

One **master** handles all writes; one or more **slaves** replicate writes asynchronously and serve read requests.

```
# In slave's redis.conf
slaveof <master-ip> <master-port>
```

Benefits: **Read scalability** (distribute reads across slaves) and **high availability** (promote a slave if the master fails).

### Strengths & Weaknesses

| Strengths | Weaknesses |
|-----------|-----------|
| Exceptional speed due to in-memory nature | Entire dataset must fit in RAM |
| Rich native data structures | Not fully durable by default — data loss possible on crash |
| Versatile: database, cache, broker, queue | Single-threaded — long commands can block the server |
| Vast ecosystem of client libraries | Native clustering requires careful setup |

---

## 10. Vector Databases & pgvector

### The Problem: Unstructured Data

Traditional relational and NoSQL databases were optimized for structured data (integers, strings, dates). Unstructured content (text, audio, video) was stored as opaque BLOBs the database couldn't interpret, limiting search to metadata (date, author, file size).

Modern architectures extract **semantic features**, enabling content-aware retrieval beyond metadata constraints.

### Semantic vs. Keyword Search

| Feature | Keyword Search | Semantic Search |
|---------|---------------|----------------|
| Mechanism | Character/string matching | Intent and context recognition |
| Strength | Exact term lookup (SKUs, names) | Understanding nuance and synonyms |
| Weakness | Misses relevant data with different wording | Inherently probabilistic |

### Vectors and Embeddings

- **Vector** — an ordered list of floating-point numbers; each float encodes a specific aspect of semantic meaning.
- **Embedding (verb)** — the process of mapping raw language to mathematical coordinates.
- **Embedding (noun)** — the resulting high-dimensional vector output.
- Context-aware: "Apple" in a fruit context produces a completely different vector than "Apple" in a tech context.

### Vector Space & Cosine Similarity

Related concepts cluster together in vector space — proximity equals semantic similarity. Cosine similarity measures the angle between two vectors:

- `cos(0°) = 1` → identical meaning.
- `cos(90°) = 0` → completely unrelated.

A short summary and a long document on the same topic are correctly recognized as similar, since **direction (meaning)** matters more than **magnitude (length)**.

### Approximate Nearest Neighbor (ANN)

Exact nearest-neighbor search requires a full dataset scan — too slow for production. ANN offers ~95% accuracy with massive speed gains, which is sufficient for GenAI use cases (which are inherently probabilistic).

### Vector Arithmetic

Because embeddings capture abstract relationships as geometric directions, semantic logic can be expressed through arithmetic:

`Vec("Queen") ≈ Vec("King") − Vec("Man") + Vec("Woman")`

### Hybrid Database Architecture

Pure vector stores lack robust metadata management. A **hybrid model** pairs:
- A **Vector Store** — similarity engine for semantic queries.
- A **Metadata Store** — relational tables for structured attributes (author, date, permissions).

Tables are connected via a foreign key ID, preventing the "overfetch-then-filter" anti-pattern.

### PostgreSQL + pgvector

pgvector is an extension that adds a native `VECTOR` data type to PostgreSQL, combining familiar relational tooling with semantic search capabilities.

**Why use pgvector:**
- Stays within the existing PostgreSQL ecosystem (backups, access controls, B-tree indexes).
- Minimal learning curve — one new data type (`VECTOR`) and a few similarity operators.
- Ideal for mid-to-large scale applications.

**Key concepts:**

```sql
-- Enable the extension
CREATE EXTENSION vector;

-- Create a table with a vector column (768 dimensions)
CREATE TABLE document_chunks (
  id          SERIAL PRIMARY KEY,
  document_id INTEGER REFERENCES documents(id) ON DELETE CASCADE,
  content     TEXT,
  embedding   VECTOR(768)
);

-- Create an HNSW index for fast similarity search
CREATE INDEX ON document_chunks
USING hnsw (embedding vector_cosine_ops);

-- Semantic similarity search (cosine distance)
SELECT content
FROM document_chunks
ORDER BY embedding <=> '[0.1, 0.2, ...]'::vector
LIMIT 5;
```

**Hybrid search** combines vector and full-text scores:
- **Vector score (70% weight):** Cosine distance via pgvector.
- **Full-text score (30% weight):** GIN-indexed `tsvector` keyword ranking.
- Results merged with a **Full Outer Join** for maximum retrieval accuracy.

### Retrieval-Augmented Generation (RAG)

RAG grounds LLM responses in private, up-to-date data to combat hallucinations and knowledge cutoff limitations.

**Workflow:** Embed query → search vector DB for relevant chunks → provide chunks to LLM as source of truth → generate grounded response.

---

## 11. Polyglot Persistence

### Concept

Polyglot persistence is the practice of using **multiple different databases**, each optimized for its specific role within the same application, rather than forcing one database to handle everything.

### The Three-Pillar Stack (RAG Example)

| Pillar | Technology | Role |
|--------|-----------|------|
| **Relational Integrity** | PostgreSQL | User management, document metadata, ACID compliance, cascading deletes. |
| **Semantic Engine** | pgvector | High-dimensional embedding storage; cosine similarity search with HNSW indexing. |
| **Performance Accelerator** | Redis | Sub-10ms caching layer for repeated AI queries; eliminates redundant LLM compute. |

### Redis Caching Strategy

**Cache key design:** `cache:user:{user_id}:{sha256_payload_hash}`

Deterministic hashing ensures identical queries from the same user always hit the same cache key.

**Event-driven cache invalidation:**
- Document upload → wildcard delete: `DEL cache:user:{user_id}:*`
- Document update → same wildcard purge.
- Document delete → wildcard purge + PostgreSQL cascade delete.

This keeps PostgreSQL (persistent) and Redis (volatile) in perfect synchronization.

### Scaling Each Layer Independently

| Layer | Strategy |
|-------|---------|
| PostgreSQL | Read replicas for distributed hybrid search load |
| pgvector | Logical partitioning by `user_id` to keep indices lean |
| Redis | Cluster mode for high-availability in-memory caching |

---

## 12. Database Genres — Choosing the Right Tool

### The Core Question

Do not ask "Can I use this database?" — ask **"Should I use this database?"**

This requires understanding each genre's inherent trade-offs and mapping them to your specific use case.

### Overview of Database Genres

| Genre | Representative Databases | Core Strength |
|-------|--------------------------|--------------|
| **Relational** | PostgreSQL, MySQL | Mathematical joins, powerful flexible querying, ACID compliance |
| **Key-Value** | Redis, DynamoDB | Blazing fast simple lookups; excellent for caching and messaging |
| **Columnar** | HBase | Adding columns is cheap; sparse rows with no null-storage cost; horizontal scalability for big data |
| **Document** | MongoDB, CouchDB | Flexible schema; nested structures; easy to evolve data models |
| **Graph** | Neo4j | Traversing complex, highly-connected networks of relationships |
| **Vector** | PostgreSQL + pgvector | Semantic similarity search; AI-ready content retrieval |

### The Relational Model Is Not Dead

Relational databases remain central to most enterprise architectures. SQL proficiency is highly sought-after, and for a vast universe of applications a relational database is still the optimal solution.

### The Rise of NoSQL

NoSQL emerged to address relational limitations at massive scale and with evolving data models. Key innovations: schemaless design, horizontal scaling, simple replication, and new query methods. NoSQL is not one product — it is a diverse category united primarily by not being relational.

### CAP Theorem

The CAP theorem states that a distributed database can only guarantee **two of the three** properties simultaneously:
- **C**onsistency — every read gets the most recent write.
- **A**vailability — every request gets a (non-error) response.
- **P**artition Tolerance — the system continues to function despite network partitions.

Understanding where each database sits on the CAP triangle is essential for architectural decision-making.

---

*IT 2008 Database Systems — Assist. Prof. Dr. Bećir ISAKOVIĆ*
