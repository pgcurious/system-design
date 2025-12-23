# Further Reading

Curated resources for deeper exploration of system design topics.

---

## Books

### Foundational
- **Designing Data-Intensive Applications** by Martin Kleppmann
  - The definitive guide to distributed systems fundamentals
  - Covers replication, partitioning, transactions, stream processing
  - Every system designer should read this

- **System Design Interview** by Alex Xu (Vol 1 & 2)
  - Practical interview-focused walkthroughs
  - Good breadth coverage, less depth

- **Database Internals** by Alex Petrov
  - Deep dive into how databases work
  - B-trees, LSM-trees, distributed consensus

### Architecture
- **Building Microservices** by Sam Newman
  - Patterns for service decomposition
  - Communication, testing, deployment strategies

- **Release It!** by Michael Nygard
  - Stability patterns: circuit breakers, bulkheads
  - War stories from production systems

---

## Papers

### Distributed Systems Foundations
- **The Google File System (2003)**
  - https://research.google/pubs/pub51/
  - Foundation for HDFS, influenced S3 design

- **Dynamo: Amazon's Highly Available Key-value Store (2007)**
  - https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf
  - Consistent hashing, eventual consistency, sloppy quorum

- **Bigtable: A Distributed Storage System (2006)**
  - https://research.google/pubs/pub27898/
  - Wide-column store design, influenced Cassandra/HBase

### Messaging & Streaming
- **Kafka: a Distributed Messaging System for Log Processing (2011)**
  - https://www.microsoft.com/en-us/research/wp-content/uploads/2017/09/Kafka.pdf
  - Original Kafka paper

- **The Log: What every software engineer should know... (2013)**
  - https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying
  - Jay Kreps on the log abstraction

### Consensus
- **In Search of an Understandable Consensus Algorithm (Raft)**
  - https://raft.github.io/raft.pdf
  - Clearer than Paxos, widely implemented

- **Paxos Made Simple** by Leslie Lamport
  - https://lamport.azurewebsites.net/pubs/paxos-simple.pdf
  - The classic consensus algorithm explained

---

## Engineering Blogs

### Big Tech
- **Stripe Engineering Blog**: https://stripe.com/blog/engineering
  - Idempotency, API design, infrastructure

- **Netflix Tech Blog**: https://netflixtechblog.com/
  - Chaos engineering, microservices, streaming

- **Uber Engineering**: https://eng.uber.com/
  - H3, real-time systems, mapping

- **Spotify Engineering**: https://engineering.atspotify.com/
  - Recommendations, data pipelines, cloud migration

- **Meta Engineering**: https://engineering.fb.com/
  - TAO, Cassandra, AI infrastructure

- **Google Cloud Blog**: https://cloud.google.com/blog/products
  - Spanner, Bigtable, GKE

### Social & Communication
- **Twitter Engineering** (archived): https://blog.twitter.com/engineering
  - Timeline architecture, snowflake IDs

- **Slack Engineering**: https://slack.engineering/
  - Real-time messaging, MySQL at scale

- **Discord Engineering**: https://discord.com/blog/engineering
  - Elixir, real-time communication

### Databases
- **PlanetScale Blog**: https://planetscale.com/blog
  - Vitess, MySQL sharding

- **CockroachDB Blog**: https://www.cockroachlabs.com/blog/
  - Distributed SQL, consistency

- **Redis Blog**: https://redis.com/blog/
  - Caching patterns, data structures

---

## Conference Talks

### Must-Watch
- **Timelines at Scale** - Raffi Krikorian (Twitter)
  - Fan-out on write vs read, hybrid approach
  - https://www.infoq.com/presentations/Twitter-Timelines-Scale/

- **Scaling Instagram Infrastructure** - Lisa Guo
  - Django at scale, cassandra, caching
  - https://www.youtube.com/watch?v=hnpzNAPiC0E

- **How Slack Works** - Keith Adams
  - Real-time architecture, MySQL sharding
  - https://www.youtube.com/watch?v=WE9c9AZe-DY

- **Building Uber's Real-Time Data Infrastructure**
  - Streaming, H3, dispatch optimization
  - https://www.youtube.com/watch?v=XRYN2xt11Ek

### Distributed Systems
- **Strangeloop Conference Archive**
  - https://www.thestrangeloop.com/archive.html
  - Excellent distributed systems talks

- **QCon Presentations**
  - https://www.infoq.com/qcon/
  - Industry practitioner talks

---

## Tools & Documentation

### Databases
- **Vitess Documentation**: https://vitess.io/docs/
- **Apache Kafka Documentation**: https://kafka.apache.org/documentation/
- **Redis Documentation**: https://redis.io/documentation

### Cloud Services
- **AWS Well-Architected Framework**: https://aws.amazon.com/architecture/well-architected/
- **Google Cloud Architecture Center**: https://cloud.google.com/architecture

### Protocols
- **AT Protocol (Bluesky)**: https://atproto.com/
- **ActivityPub (Mastodon)**: https://www.w3.org/TR/activitypub/

---

## Courses

### Free
- **MIT 6.824: Distributed Systems**
  - https://pdos.csail.mit.edu/6.824/
  - Labs implement Raft, sharded KV store

- **Designing Data-Intensive Applications Study Guide**
  - https://github.com/keyvanakbary/learning-notes/blob/master/books/designing-data-intensive-applications.md
  - Community notes on DDIA

### Paid
- **Grokking the System Design Interview** (Educative)
  - https://www.educative.io/courses/grokking-the-system-design-interview
  - Popular interview prep

- **System Design Primer** (GitHub)
  - https://github.com/donnemartin/system-design-primer
  - Free, comprehensive, community-maintained

---

## Podcasts

- **Software Engineering Daily**
  - https://softwareengineeringdaily.com/
  - Deep technical interviews

- **The Changelog**
  - https://changelog.com/podcast
  - Open source and infrastructure

- **Acquired**
  - https://www.acquired.fm/
  - Business history of tech companies (includes architecture discussions)
