# System Design Case Studies: A Deep Dive

> *Understanding not just "what" these systems do, but "why" they were designed this way and "how" they work at both the application and infrastructure layers.*

---

## Who This Book Is For

You're a backend engineer with 5+ years of experience. You've built CRUD apps, scaled databases, and maybe even survived a production outage at 3 AM. But when someone asks, "How would you design WhatsApp?" you feel that familiar knot in your stomach.

This book is for you.

We won't teach you how to write code—you already know that. Instead, we'll dissect 18 real-world systems, layer by layer, from the high-level architecture down to the metal. By the end, you'll understand:

- **Why** Uber uses hexagonal grids instead of traditional geospatial indexes
- **How** Kafka achieves exactly-once delivery (and why it took them years)
- **What** happens inside a stock exchange when a trade executes in under a microsecond
- **When** to fan-out on write vs. read (and why Twitter uses both)

## How to Read This Book

### The Linear Path
If you're new to distributed systems, start from Chapter 1 and work forward. Each part builds on concepts from previous chapters:

1. **Part 1: Foundations** - Core primitives: storage, messaging, simple services
2. **Part 2: Real-time Communication** - Persistent connections, delivery guarantees
3. **Part 3: Content at Scale** - Media serving, database sharding, collaboration
4. **Part 4: Social Feeds** - Fan-out patterns, ranking, decentralization
5. **Part 5: Matching & Discovery** - Geospatial systems, real-time matching
6. **Part 6: Financial Systems** - Consistency guarantees, idempotency
7. **Part 7: AI & Serverless** - GPU infrastructure, function scheduling
8. **Part 8: Capstone** - Putting it all together

### The Reference Path
If you're preparing for a specific interview or researching a particular system, jump directly to the relevant chapter. Each chapter is self-contained with cross-references to related concepts.

### The Deep Dive Path
For each chapter, we include:
- **Thought Experiments** - Questions that test deep understanding
- **Failure Scenarios** - What breaks, and how systems recover
- **Historical Evolution** - How designs changed over time

## Prerequisites

You should be comfortable with:

- **Databases**: SQL, indexes, transactions, replication (we'll build on this)
- **Networking**: TCP vs UDP, HTTP, DNS, load balancers (conceptually)
- **Basic distributed concepts**: CAP theorem, eventual consistency (we'll deepen this)
- **Reading architecture diagrams**: We use Mermaid heavily

You don't need:
- Experience with specific technologies (we explain Kafka, Redis, etc. from scratch)
- Knowledge of specific programming languages (no code, only pseudocode)
- Prior system design interview practice (this book is the practice)

## Our Philosophy

### Infrastructure Matters
Most system design resources stop at "use a cache" or "add a load balancer." We go deeper:
- How many servers? What specs?
- What's the network topology?
- How much does it cost?

### Failure Is the Default State
In distributed systems, something is always failing. Every chapter includes:
- What happens when component X dies?
- How is failure detected?
- What data is at risk?

### Numbers Build Intuition
We include concrete scale numbers throughout:
- Requests per second
- Data volumes
- Latency budgets
- Memory requirements

### Tradeoffs Over Best Practices
There are no "best practices" in system design—only tradeoffs. Every decision has costs. We make these explicit.

## A Note on Sources

Wherever possible, we cite primary sources:
- Engineering blog posts from the companies themselves
- Conference talks by the engineers who built these systems
- Academic papers underlying the technologies

Where information is speculative or inferred, we say so explicitly.

## Quick Reference: The Systems

| Chapter | System | Core Challenge | Key Insight |
|---------|--------|---------------|-------------|
| 01 | Amazon S3 | 11 nines of durability | Math of replication across failure domains |
| 02 | Kafka | Exactly-once messaging | Producer IDs + sequence numbers |
| 03 | URL Shortener | High read/write ratio | When NOT to optimize |
| 04 | WhatsApp | 2M connections/server | Erlang's actor model fits chat |
| 05 | Slack | Workspace isolation | Multi-tenancy at the database layer |
| 06 | YouTube/Vitess | Sharding relational DBs | Two-level sharding + VIndex |
| 07 | Spotify | Audio streaming | Adaptive bitrate + offline sync |
| 08 | Google Docs | Real-time collaboration | OT vs CRDT (they chose OT) |
| 09 | Twitter Timeline | Celebrity problem | Hybrid fan-out |
| 10 | Reddit | Vote counting at scale | Eventual consistency is okay |
| 11 | Bluesky | Account portability | DIDs + AT Protocol |
| 12 | Tinder | Geospatial matching | Geohash vs S2 cells |
| 13 | Uber Drivers | Real-time location | H3 hexagonal indexing |
| 14 | Stock Exchange | Sub-millisecond matching | Kernel bypass + FPGAs |
| 15 | Stripe | Idempotent payments | Request deduplication patterns |
| 16 | ChatGPT | GPU utilization | Batching + KV cache |
| 17 | Meta Serverless | 11.5M functions/sec | Cold start elimination |
| 18 | 10M Users | Progressive scaling | When to add complexity |

---

## Let's Begin

Turn to [Chapter 1: Amazon S3](./part-1-foundations/01-amazon-s3/README.md) to understand how the world's most reliable storage system achieves those mythical eleven nines of durability.

---

*This book is a living document. Systems evolve, and so will these chapters.*
