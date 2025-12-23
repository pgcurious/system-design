# Glossary

Technical terms used throughout this book, defined in the context of system design.

---

## A

### ACID
**Atomicity, Consistency, Isolation, Durability**. The four properties that guarantee database transactions are processed reliably. Critical for financial systems but often relaxed for scale.

### Actor Model
A concurrency model where "actors" are independent units that communicate via message passing. Used by Erlang (WhatsApp) and Akka. Each actor has its own state and processes messages sequentially.

### Adaptive Bitrate Streaming (ABR)
Video/audio streaming that adjusts quality based on network conditions. Used by Spotify, Netflix, YouTube. Client measures bandwidth and requests appropriate quality chunks.

### Availability Zone (AZ)
A physically separate data center within a cloud region. AWS, GCP, and Azure all use this concept. Replicating across AZs protects against facility-level failures.

---

## B

### Base62
Encoding using 62 characters (a-z, A-Z, 0-9). Used for URL shorteners because all characters are URL-safe. 7 Base62 characters = 3.5 trillion unique values.

### Bloom Filter
A probabilistic data structure that tests set membership. Can return false positives but never false negatives. Used by Tinder for "already swiped" checks.

### Broker
In message queues, the server that receives, stores, and delivers messages. Kafka brokers hold partitions; RabbitMQ brokers hold queues.

---

## C

### CAP Theorem
States that a distributed system can only guarantee two of: **Consistency**, **Availability**, **Partition tolerance**. In practice, partition tolerance is required, so you choose between CP and AP.

### CDN (Content Delivery Network)
Geographically distributed servers that cache content close to users. Cloudflare, Fastly, Akamai are examples. Reduces latency and origin server load.

### Cell (H3/Geohash)
A discrete geographic region used for spatial indexing. H3 uses hexagons; geohash uses rectangles. Users in the same cell are "nearby" for query purposes.

### Circuit Breaker
A pattern that prevents cascading failures by "opening" (stopping requests) when a downstream service fails repeatedly. Named after electrical circuit breakers.

### Commit Log
An append-only sequence of records. Kafka is built around this abstraction. Also called write-ahead log (WAL) in databases.

### Consistent Hashing
A hashing technique where adding/removing nodes only affects K/n keys (not all keys). Essential for distributed caches and databases. Used by S3, DynamoDB.

### CRDT (Conflict-free Replicated Data Type)
A data structure that can be replicated across multiple computers, updated independently, and always merged deterministically. Alternative to OT for collaborative editing.

---

## D

### Dead Letter Queue (DLQ)
A queue for messages that couldn't be processed after multiple attempts. Allows manual inspection and replay without blocking the main queue.

### DID (Decentralized Identifier)
A globally unique identifier that doesn't require a central registry. Used by Bluesky for portable identity. Format: `did:method:specific-id`.

### Durability
The guarantee that once a transaction commits, it survives crashes. The "D" in ACID. Achieved through write-ahead logs and replication.

---

## E

### ELO Rating
A rating system originally for chess. Higher-rated player beats lower-rated = small rating change. Upset = large change. Tinder uses a variant for user desirability.

### Erasure Coding
A technique to encode data into fragments with redundancy, allowing reconstruction from a subset of fragments. More storage-efficient than simple replication. Used by S3 for cold storage.

### Eventual Consistency
A consistency model where updates propagate asynchronously. Reads may return stale data temporarily, but all replicas eventually converge. Reddit vote counts are eventually consistent.

---

## F

### Fan-out
Distributing a message or update to multiple recipients. "Fan-out on write" means pre-computing at write time; "fan-out on read" means computing at read time. Twitter uses both.

### FPGA (Field-Programmable Gate Array)
Hardware that can be programmed to perform specific operations very fast. Used by stock exchanges for nanosecond-level matching.

---

## G

### Geohash
A hierarchical spatial indexing system that encodes latitude/longitude into a string. Nearby points share prefixes. `dr5ru` is New York City at resolution 5.

---

## H

### H3
Uber's hexagonal hierarchical spatial index. Divides Earth into hexagonal cells at multiple resolutions. Better than geohash for consistent neighbor distances.

### Hot Partition/Hot Key
A partition or key receiving disproportionate traffic. Causes load imbalance in distributed systems. Celebrity accounts on Twitter create hot partitions.

---

## I

### Idempotency
The property that multiple identical requests have the same effect as a single request. Critical for payment systems. Stripe uses idempotency keys for this guarantee.

### In-Sync Replica (ISR)
In Kafka, a replica that is fully caught up with the leader. Messages are only considered committed when written to all ISR members.

---

## J

### Journal
A durable, ordered log of all operations. Stock exchanges use journals for audit trails. Can be replayed to reconstruct system state.

---

## K

### KV Cache
Key-value cache for transformer model inference. Stores computed attention keys and values to avoid recomputation. Critical for ChatGPT-style streaming.

### Kernel Bypass
Networking techniques (like DPDK) that skip the operating system kernel for lower latency. Used by stock exchanges.

---

## L

### Leader Election
The process of selecting one node to coordinate others. Kafka uses ZooKeeper/KRaft for leader election. Critical for consensus.

### Lexicon
In AT Protocol (Bluesky), a schema definition for data types. Similar to JSON Schema but with namespacing.

---

## M

### Matching Engine
The core component of a stock exchange that matches buy and sell orders. Must be deterministic and extremely fast.

### Message Broker
Middleware that handles message routing between producers and consumers. Kafka, RabbitMaker, Redis Pub/Sub are examples.

### Microservice
A small, independently deployable service that does one thing. Contrast with monolith. Communication via APIs or message queues.

### Multicast
Network protocol that delivers to multiple recipients simultaneously. Efficient for market data distribution. One-to-many, unlike unicast (one-to-one).

---

## N

### NAT (Network Address Translation)
Technique that maps private IP addresses to public ones. Complicates peer-to-peer connections (why WebRTC needs STUN/TURN servers).

---

## O

### OT (Operational Transformation)
An algorithm for resolving concurrent edits in collaborative systems. Used by Google Docs. Transforms operations to account for others' changes.

### Order Book
The data structure in a stock exchange that holds all active buy and sell orders, organized by price level.

---

## P

### Partition
A division of data for distribution. Kafka topics have partitions; databases have shards. Partition key determines which partition holds each record.

### PDS (Personal Data Server)
In Bluesky/AT Protocol, the server that stores a user's data. Can be self-hosted or provided by a service.

### PTP (Precision Time Protocol)
Protocol for synchronizing clocks across a network to sub-microsecond accuracy. Required by financial regulations.

---

## Q

### Quorum
The minimum number of nodes that must agree for an operation to succeed. Typically `(N/2) + 1` for N nodes. Ensures consistency despite failures.

---

## R

### Raft
A consensus algorithm for maintaining a replicated log across multiple servers. Used by etcd, CockroachDB. More understandable than Paxos.

### Replica
A copy of data on another node. Read replicas handle read traffic; replicas provide durability. Replication lag is the delay between primary and replica.

### Ring Buffer
A fixed-size buffer that overwrites oldest entries when full. Used for ephemeral data like Uber driver locations.

---

## S

### Saga Pattern
A pattern for managing distributed transactions as a sequence of local transactions with compensating actions for rollback.

### Shard
A horizontal partition of a database. Each shard holds a subset of data. Vitess shards MySQL by keyspace.

### Signal Protocol
The cryptographic protocol for end-to-end encryption used by WhatsApp and Signal. Provides forward secrecy via the Double Ratchet algorithm.

### Snowflake ID
A 64-bit unique ID format: timestamp + machine ID + sequence number. Sortable by time, no coordination needed. Created by Twitter.

### Strong Consistency
Every read sees the most recent write. Contrast with eventual consistency. More expensive but simpler to reason about.

### Surge Pricing
Dynamic pricing that increases when demand exceeds supply. Uber's mechanism to balance the marketplace during peak times.

---

## T

### Tombstone
A marker indicating a deleted record. In systems that don't immediately remove data (like Cassandra, CRDTs), tombstones prevent resurrection of deleted data.

### Two-Phase Commit (2PC)
A protocol for atomic commits across multiple databases. Phase 1: Prepare (can you commit?). Phase 2: Commit (do it). Blocking if coordinator fails.

---

## V

### VIndex
Vitess's secondary index that maps non-sharding keys to sharding keys. Enables efficient queries on columns other than the shard key.

### VTGate
Vitess's query router. Accepts MySQL protocol, routes queries to appropriate shards, aggregates results.

---

## W

### WAL (Write-Ahead Log)
A log where changes are written before being applied to the main data store. Ensures durability; enables recovery after crashes.

### WebSocket
A protocol for bidirectional communication over a single TCP connection. Used by Slack, WhatsApp for real-time messaging.

---

## Z

### ZooKeeper
A distributed coordination service for maintaining configuration, naming, and providing distributed synchronization. Used by older Kafka deployments.
