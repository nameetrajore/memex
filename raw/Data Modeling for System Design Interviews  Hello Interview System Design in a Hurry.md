---
title: "Data Modeling for System Design Interviews | Hello Interview System Design in a Hurry"
source: "https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling"
author:
published:
created: 2026-05-13
description: "Learn about data modeling for system design interviews"
tags:
  - "clippings"
---
###### Core Concepts

## Data Modeling

---

Data modeling is the process of defining how your application’s data is structured, stored, and related. In practice, this means deciding what entities exist, how they’re identified, and how they connect to one another. For a system design interview, the bar is much lower than in a dedicated data modeling interview (commonplace in data engineering loops). You’re not expected to normalize everything or produce a complete schema diagram, you’re just expected to design something clear, functional, and aligned with your system’s requirements.

In the [Delivery framework](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery), this comes up twice. First, during requirements gathering, you’ll identify your [core entities](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery#core-entities-2-minutes). These usually map 1:1 with tables or collections and form the backbone of your schema. Later, in the [High-Level Design step](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery#high-level-design-10-minutes), you'll sketch a basic schema alongside your database component. Include the key fields, relationships, and a note on how you'd index or partition to support the main query patterns. That’s enough for most interviewers to see that your data model won’t crumble under expected usage.

Data Modeling in an Interview

Still, a reasonable schema is more than box-drawing. It sets up the rest of your design, such as scaling reads and writes, preserving consistency when it matters, and answering questions about growth or auditability without backtracking. A sloppy data model can lead to painful issues later. A solid, “good enough” one lets the conversation stay focused where it belongs.

## Database Model Options

Before you can design a schema, you need to pick what type of database you're working with. Different database models shape how you structure your data, so this choice affects everything that follows.

In interviews, the temptation is to show off by choosing exotic database types. Resist this. Most of the time, the right answer is a relational database. It's the default unless your requirements clearly signal a specialized model. Unless you have significant experience and, with it, strong opinions about another database, my recommendation is to stick with [PostgreSQL](https://www.hellointerview.com/learn/system-design/deep-dives/postgres).

That doesn’t mean other database models aren’t worth knowing. Showing you understand when they might be useful demonstrates that you’re thinking about trade-offs, not just parroting the default. Still, the star of the show is SQL, so we’ll start there before briefly touching on the alternatives.

### Relational Databases (SQL)

Relational databases organize data into tables with fixed schemas, where rows represent entities and columns represent attributes. They enforce relationships through [foreign keys](#entities-keys-relationships) and provide [ACID](https://en.wikipedia.org/wiki/ACID) guarantees for transactions.

Most system design problems map naturally onto this model. A social media app has users, posts, comments, and likes, all entities with clear relationships. An e-commerce system has users, products, orders, and payments. These fit neatly into relational tables where constraints and [foreign keys](#entities-keys-relationships) preserve integrity.

**Users table:**

| id (primary key) | username | email | created\_at |
| --- | --- | --- | --- |
| 1 | john\_doe | [john@example.com](mailto:john@example.com) | 2024-01-01 10:00:00 |
| 2 | jane\_doe | [jane@example.com](mailto:jane@example.com) | 2024-01-01 10:05:00 |
| 3 | bob\_smith | [bob@example.com](mailto:bob@example.com) | 2024-01-01 10:10:00 |

**Posts table:**

| id (primary key) | user\_id (foreign key) | content | created\_at |
| --- | --- | --- | --- |
| 1 | 1 | Hello, world! | 2024-01-01 10:00:00 |
| 2 | 1 | My first post | 2024-01-01 10:05:00 |
| 3 | 2 | Another post | 2024-01-01 10:10:00 |

**Likes table:**

SQL is great at handling complex queries. If you need to fetch "all posts by users that a given user follows, ordered by recency," joins make that straightforward. However, be careful with multi-table joins like this - they can become performance traps at scale. In interviews, mentioning complex reporting-style queries often raises yellow flags about performance, so you'll want to think through whether they're actually performant enough or if you need denormalized views, caching, or pre-computed results. And when strong consistency is a [non-functional requirement](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery#non-functional-requirements), like ensuring payments don't double-charge or inventory doesn't oversell, SQL's ACID guarantees are the right tool for the job.

The usual knock on relational databases is scalability, but this is often exaggerated. Modern SQL databases scale with techniques like read replicas, sharding, connection pooling, and caching. Some of the largest companies in the world (Facebook, Airbnb) rely on relational foundations. Scaling isn't just about the database you choose, but how you architect around it.

**Example technologies include** [PostgreSQL](https://www.hellointerview.com/learn/system-design/deep-dives/postgres), [MySQL](https://www.mysql.com/), and [SQLite](https://www.sqlite.org/index.html).

### Document Databases

Document databases store data as JSON-like documents with flexible schemas, making them good for rapidly evolving applications where you don't know all your data fields upfront. Your data modeling becomes more about nesting and embedding related information within documents rather than normalizing across tables.

Notice how the same data from our SQL example gets restructured. Instead of separate tables, we embed posts directly within each user document. This eliminates joins but means updating a post requires finding and modifying the entire user document.

**Users collection:**

```
{
  "_id": "507f1f77bcf86cd799439011",
  "username": "john_doe",
  "email": "john@example.com",
  "posts": [
    {
      "content": "Hello, world!",
      "created_at": "2024-01-01T10:00:00Z"
    },
    {
      "content": "My first post",
      "created_at": "2024-01-01T10:05:00Z"
    }
  ],
  "created_at": "2024-01-01T10:00:00Z"
}
```

System design interviews intentionally scope functional requirements to a clear, concise set. This means you're unlikely to have "evolving schemas" in the first place, which removes the main reason to choose document databases. Only consider them if your interviewer explicitly mentions rapidly changing data structures.

**When to consider over SQL:** When your schema changes frequently, when you have deeply nested data that would require many joins in SQL, or when different records have vastly different structures. A user profile system where some users have extensive work histories while others have minimal data fits this pattern.

**Data modeling impact:** You'll denormalize more aggressively, embedding related data within documents to avoid expensive lookups across collections. This trades storage space and update complexity for read performance.

**Example technologies include** MongoDB, Firestore, and CouchDB.

### Key-Value Stores

Key-value stores provide simple lookups where you fetch values by exact key match. They're extremely fast but offer limited query capabilities beyond that basic operation.

**When to consider over SQL:** For caching, session storage, feature flags, or any scenario where you only need to look up data by a single identifier. They're also good for high-write scenarios where you need maximum performance and don't need complex queries.

"Over SQL" is misleading here. In practice, you'll often use both together. SQL as your source of truth with a key-value cache (like Redis) in front for hot data. This gives you fast access without sacrificing durability or complex queries.

**Data modeling impact:** Your schema becomes very flat. You'll [denormalize](#normalization-vs-denormalization) heavily and duplicate data across multiple keys to support different access patterns, since you can't join or query across relationships. This is great for reads but terrible for consistency when data changes.

Key-Value Cache

**Example technologies include** [Redis](https://www.hellointerview.com/learn/system-design/deep-dives/redis), [DynamoDB](https://www.hellointerview.com/learn/system-design/deep-dives/dynamodb), and [Memcached](https://memcached.org/).

### Wide-Column Databases

Wide-column databases organize data into column families where rows can have different sets of columns. They're optimized for massive write-heavy workloads and time-series data.

When a user creates a new post, you add a new row keyed by (user\_id, timestamp). Rows with the same partition key (user\_id) are stored together, making writes fast (just append to the partition) and reads efficient (scan a contiguous range of a user's posts).

Wide-Column Database

**When to consider over SQL:** When you have enormous write volumes, time-series data, or analytics workloads where you primarily append data and run aggregations. Think telemetry, event logging, or IoT sensor data.

**Data modeling impact:** You'll design around query patterns even more than with SQL, often duplicating data across different column families to support various access patterns. Time becomes a first-class citizen in your modeling.

**Example technologies include** [Cassandra](https://www.hellointerview.com/learn/system-design/deep-dives/cassandra) and [HBase](https://hbase.apache.org/).

### Graph Databases

Graph databases store data as nodes and edges, optimizing for traversing relationships between entities.

Graph Database

**When to consider over SQL:** Honestly? Almost never in interviews. The classic examples are social networks and recommendation engines, but even Facebook models their social graph with MySQL. If it's good enough for the world's largest social network, it's probably good enough for your interview.

**Example technologies include** [Neo4j](https://neo4j.com/) and [Amazon Neptune](https://aws.amazon.com/neptune/).

Graph databases are a common mistake in interviews. They sound sophisticated but add unnecessary complexity. Even "graph-heavy" companies like LinkedIn and Twitter use SQL for their core relationship data. Other databases can handle the primary query patterns without the operational complexity that comes with specialized graph systems.

## Schema Design Fundamentals

Once you've picked your database type, you need to design a schema that supports your system's requirements.

### Start with Requirements

Everything flows from three key factors that require careful consideration and were likely already determined during the [requirements gathering](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery#requirements-5-minutes) and [api design](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery#api-or-system-interface) phases.

**Data volume** determines where your data can physically live. A social media app with millions of users might need data spread across multiple data stores, which drives schema design choices. If user data and post data need to live on separate systems for performance or organizational reasons, they necessarily need distinct schemas with careful consideration of how they reference each other.

**Access patterns** are the most important factor and drive most of your design decisions. How will your data be queried? A news feed that loads "recent posts by followed users" suggests you'll want denormalized data or carefully designed indexes. An analytics dashboard that aggregates data across time periods might need different table structures entirely. This comes naturally from your APIs. Just ask what queries will I need to support each endpoint?

**Consistency requirements** determine how tightly coupled your data can be. Financial transactions need strong consistency (no partial charges), which often means keeping related data in the same database with ACID guarantees. But a user's activity feed can handle eventual consistency (it's okay if a like shows up a few seconds later), which allows you to distribute that data across separate systems with different schemas optimized for different access patterns.

In interviews, explicitly tie your schema choices back to these factors. For example, *"Since we need to load feeds quickly and likes can be eventually consistent, I'll denormalize like counts into the posts table."* That shows you're reasoning instead of memorizing patterns.

All of the schema design techniques that follow (entities, keys, normalization, indexes, sharding) are just tools to address these three factors.

### Entities, Keys & Relationships

Once you’ve identified your [core entities](https://www.hellointerview.com/learn/system-design/in-a-hurry/delivery#core-entities-2-minutes), the next step is to map them into tables (or collections) with clear identifiers and relationships.

For a social media app, you might have users, posts,, and. Each entity needs a to identify individual records. Use system-generated IDs like or rather than business data like email addresses. System-generated keys stay stable even when business rules change.