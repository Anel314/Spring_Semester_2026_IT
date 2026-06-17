# Database Cheatsheet: PostgreSQL · MongoDB · Redis

---

## PostgreSQL

### Views

```sql
-- Create a simple view
CREATE VIEW available_books AS
SELECT title, author, available_copies
FROM books;

-- Query it like a table
SELECT * FROM available_books;

-- Update through an updatable view
UPDATE available_books
SET available_copies = 5
WHERE title = 'Deep Work';

-- Replace an existing view
CREATE OR REPLACE VIEW available_books AS
SELECT title, author, available_copies, category
FROM books;

-- Drop a view
DROP VIEW available_books;
```

```sql
-- Materialized view (stores result physically)
CREATE MATERIALIZED VIEW book_count_by_category AS
SELECT category, SUM(available_copies) AS total
FROM books
GROUP BY category;

-- Must refresh manually after data changes
REFRESH MATERIALIZED VIEW book_count_by_category;

SELECT * FROM book_count_by_category;
```

---

### Full-Text Search

```sql
-- Basic search: find rows where body contains 'database' AND 'system'
SELECT title
FROM articles
WHERE to_tsvector('english', body) @@ to_tsquery('english', 'database & system');

-- Logical operators: AND (&), OR (|), NOT (!)
SELECT title
FROM articles
WHERE to_tsvector('english', body) @@ to_tsquery('english', 'PostgreSQL & (database | indexing)');

-- Phrase search (words must appear in order)
SELECT title
FROM articles
WHERE to_tsvector('english', body) @@ phraseto_tsquery('english', 'full text search');

-- Rank results by relevance
SELECT title,
       ts_rank(to_tsvector('english', body), to_tsquery('english', 'database')) AS rank
FROM articles
WHERE to_tsvector('english', body) @@ to_tsquery('english', 'database')
ORDER BY rank DESC;
```

```sql
-- For large tables: precompute and index the tsvector
ALTER TABLE articles ADD COLUMN search_vector tsvector;

UPDATE articles
SET search_vector = to_tsvector('english', title || ' ' || body);

CREATE INDEX idx_articles_fts ON articles USING GIN(search_vector);

-- Now search uses the index
SELECT title
FROM articles
WHERE search_vector @@ to_tsquery('english', 'PostgreSQL & search');
```

---

### Multidimensional Aggregation (CUBE & ROLLUP)

```sql
-- CUBE: all combinations of dimensions + grand total
-- NULL values represent aggregated/subtotal rows
SELECT region, product, SUM(total_amount) AS total_sales
FROM sales
GROUP BY CUBE(region, product)
ORDER BY region, product;

-- Replace NULLs with readable labels
SELECT
    COALESCE(region, 'All Regions') AS region,
    COALESCE(product, 'All Products') AS product,
    SUM(total_amount) AS total_sales
FROM sales
GROUP BY CUBE(region, product)
ORDER BY region, product;
```

```sql
-- ROLLUP: hierarchical subtotals (removes right column at each level)
-- Useful for Year → Month → Day, Country → Region → City
SELECT
    COALESCE(region, 'All Regions') AS region,
    COALESCE(product, 'All Products') AS product,
    SUM(total_amount) AS total_sales
FROM sales
GROUP BY ROLLUP(region, product)
ORDER BY region, product;
```

| Output row | CUBE | ROLLUP |
|---|---|---|
| (region, product) | ✅ | ✅ |
| (region, ALL) | ✅ | ✅ |
| (ALL, product) | ✅ | ❌ |
| Grand total | ✅ | ✅ |

---

---

## MongoDB

### Basic CRUD

```js
// Find all documents
db.listingsAndReviews.find({})

// Filter
db.listingsAndReviews.find({ cancellation_policy: "flexible" })

// Projection: include name, exclude _id
db.listingsAndReviews.find(
  { cancellation_policy: "flexible" },
  { _id: 0, name: 1 }
)

// Find one
db.listingsAndReviews.findOne({ name: "Beach House" })

// Insert
db.listingsAndReviews.insertOne({ name: "My Place", price: 80 })

// Delete
db.listingsAndReviews.remove({ name: "Copacabana Apartment Posto 6" })

// Sort, skip, limit
db.listingsAndReviews.find({}).sort({ price: -1 }).limit(10).skip(5)
```

---

### Query Operators

```js
// Comparison
db.col.find({ price: { $gt: 100 } })          // greater than
db.col.find({ price: { $gte: 100 } })         // greater than or equal
db.col.find({ price: { $lt: 200 } })          // less than
db.col.find({ price: { $ne: 0 } })            // not equal
db.col.find({ status: { $in: ["a","b"] } })   // value in array

// Logical
db.col.find({ $and: [{ price: { $gt: 100 } }, { price: { $lt: 200 } }] })
db.col.find({ $or:  [{ category: "Fiction" }, { category: "History" }] })
db.col.find({ $nor: [{ price: { $lt: 0 } }] })
```

---

### Aggregation Pipeline

Each stage transforms the documents; output of one stage feeds the next.

```js
// Stage reference
// $match   → filter (like WHERE)
// $group   → aggregate (like GROUP BY)
// $project → select/reshape fields (like SELECT)
// $sort    → order results
// $limit   → take first N
// $skip    → skip first N
// $unwind  → flatten array field into individual documents
// $lookup  → join another collection
```

```js
// Average price per property type
db.listingsAndReviews.aggregate([
  {
    $group: {
      _id: "$property_type",
      averagePrice: { $avg: "$price" }
    }
  }
])

// Filter first, then group, then sort
db.listingsAndReviews.aggregate([
  { $match: { bedrooms: { $gte: 2 } } },
  {
    $group: {
      _id: "$property_type",
      averagePrice: { $avg: "$price" },
      count: { $sum: 1 }
    }
  },
  { $sort: { averagePrice: -1 } }
])

// Unwind array, count each element
db.listingsAndReviews.aggregate([
  { $unwind: "$amenities" },
  {
    $group: {
      _id: "$amenities",
      count: { $sum: 1 }
    }
  },
  { $sort: { count: -1 } }
])

// Top 5 amenities per market
db.listingsAndReviews.aggregate([
  { $unwind: "$amenities" },
  {
    $group: {
      _id: { market: "$address.market", amenity: "$amenities" },
      count: { $sum: 1 }
    }
  },
  { $sort: { count: -1 } },
  {
    $group: {
      _id: "$_id.market",
      amenities: { $push: { amenity: "$_id.amenity", count: "$count" } }
    }
  },
  {
    $project: {
      _id: 1,
      top5: { $slice: ["$amenities", 5] }
    }
  }
])
```

### Aggregation Expressions Quick Reference

| Expression | Description | Example |
|---|---|---|
| `$sum` | Total of field values | `{ $sum: "$bedrooms" }` |
| `$avg` | Average | `{ $avg: "$price" }` |
| `$min` / `$max` | Min or max value | `{ $min: "$price" }` |
| `$count` | Count documents | `{ $sum: 1 }` |
| `$push` | Collect into array (allows duplicates) | `{ $push: "$name" }` |
| `$addToSet` | Collect unique values | `{ $addToSet: "$tag" }` |
| `$first` / `$last` | First or last value in group | `{ $first: "$date" }` |

---

---

## Redis

### Key Naming Convention

```
entity:id               → student:1001
entity:id:attribute     → student:1001:gpa
entity:id:relationship  → student:1001:courses
```

---

### Strings

```redis
SET total_students 100
GET total_students

-- Atomic increment / decrement
INCR total_students
DECR total_students

-- Store with automatic expiry (30 minutes)
SET session:1001 "active" EX 1800
```

---

### Hashes (objects)

```redis
-- Set multiple fields at once
HSET student:1001 name "Alice Brown" year 2 gpa 3.6 status "active"

-- Get a single field
HGET student:1001 name

-- Get all fields and values
HGETALL student:1001
```

---

### Lists (ordered, allows duplicates)

```redis
-- Push to end / front
RPUSH student:1001:activity "Logged in"
RPUSH student:1001:activity "Enrolled in CS101"
LPUSH student:1001:activity "Urgent notice"

-- Get range (0 -1 = all elements)
LRANGE student:1001:activity 0 -1

-- Get last 5 actions
LRANGE student:1001:activity -5 -1
```

---

### Sets (unique, unordered)

```redis
-- Add members
SADD department:CS:students 1001 1002 1003

-- Get all members
SMEMBERS department:CS:students

-- Check membership (returns 1 or 0)
SISMEMBER department:CS:students 1001

-- Count members
SCARD department:CS:students
```

---

### Sorted Sets (unique members with a numeric score)

```redis
-- Add member with score
ZADD course:CS101:grades 88 1001
ZADD course:CS101:grades 92 1002

-- Get all, lowest score first
ZRANGE course:CS101:grades 0 -1 WITHSCORES

-- Get all, highest score first (leaderboard)
ZREVRANGE course:CS101:grades 0 -1 WITHSCORES

-- Get top 3
ZREVRANGE course:CS101:grades 0 2 WITHSCORES

-- Get by score range
ZRANGEBYSCORE course:CS101:grades 90 +inf WITHSCORES

-- Increment score (e.g. popularity)
ZINCRBY page:views 1 page:501
```

---

### TTL (Expiry)

```redis
-- Set expiry on an existing key
EXPIRE session:1001 1800

-- Check remaining time (-1 = no expiry, -2 = key doesn't exist)
TTL session:1001

-- Remove expiry
PERSIST session:1001
```

---

### Key Management

```redis
EXISTS student:1001        -- returns 1 (exists) or 0
DEL student:1001           -- delete key
TYPE student:1001          -- returns: string / hash / list / set / zset
```

---

### Transactions

All commands between MULTI and EXEC are queued and run atomically.

```redis
MULTI
  SADD student:1002:courses CS102
  SADD course:CS102:students 1002
  RPUSH student:1002:activity "Enrolled in CS102"
EXEC
```

---

### Publish / Subscribe

```redis
-- Terminal 1: subscribe
SUBSCRIBE enrollment_events

-- Terminal 2: publish
PUBLISH enrollment_events "Student 1001 enrolled in CS101"
```

---

### Modeling Relationships in Redis

```redis
-- Many-to-many: student ↔ courses (bidirectional sets)
SADD student:1001:courses CS101
SADD course:CS101:students 1001

-- One-to-many: department → students
SADD department:CS:students 1001 1002

-- Foreign key equivalent (string pointing to an ID)
SET course:CS101:instructor 5001
```

---

## Quick Comparison

| Concept | PostgreSQL | MongoDB | Redis |
|---|---|---|---|
| Filter | `WHERE` | `$match` / `find({...})` | Key pattern or Set lookup |
| Aggregate | `GROUP BY` + `SUM/AVG` | `$group` + `$sum/$avg` | `ZADD` / `ZINCRBY` |
| Sort | `ORDER BY col DESC` | `.sort({field:-1})` / `$sort` | `ZREVRANGE` |
| Limit | `LIMIT 5` | `.limit(5)` / `$limit` | `LRANGE key 0 4` |
| Join | `JOIN ... ON` | `$lookup` | Manual bidirectional sets |
| Index | `CREATE INDEX ... USING GIN` | Automatic on `_id`; manual otherwise | Sorted Sets as indexes |
| Expire data | — | — | `EXPIRE key seconds` |
| Transactions | `BEGIN / COMMIT` | Sessions (4.0+) | `MULTI / EXEC` |
