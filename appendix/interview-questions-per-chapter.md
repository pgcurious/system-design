# Interview Questions by Chapter

Five questions per chapter: 2 conceptual, 2 design, 1 estimation.

---

## Chapter 1: Amazon S3

### Conceptual
1. **Why does S3 use erasure coding for cold storage but replication for hot storage?**
   - Expected: Discuss tradeoffs—erasure coding is more storage-efficient but requires compute to reconstruct; replication is faster to read but costs 3x storage.

2. **Explain how consistent hashing enables horizontal scaling with minimal data movement.**
   - Expected: Virtual nodes on a ring, adding a node affects only 1/N of data, contrast with modulo hashing.

### Design
3. **How would you design the metadata service for S3? Consider that every GET request needs to look up object location.**
   - Expected: Discussion of consistent hashing for metadata sharding, caching strategies, handling billions of objects.

4. **S3 offers 11 nines of durability. If you wanted to offer 8 nines instead at lower cost, what would you change?**
   - Expected: Fewer replicas, single-region, less redundancy across AZs, math showing the durability difference.

### Estimation
5. **S3 stores 280 trillion objects. Estimate the metadata storage requirement.**
   - Expected: ~500 bytes metadata per object = 140 PB of metadata. Discuss indexing overhead.

---

## Chapter 2: Apache Kafka

### Conceptual
1. **Why did Kafka choose to let followers pull from the leader, rather than having the leader push?**
   - Expected: Pull allows followers to consume at their own pace, avoids overwhelming slow followers, simplifies flow control.

2. **Exactly-once semantics in Kafka requires producer IDs and sequence numbers. Why can't you achieve exactly-once with just idempotent consumers?**
   - Expected: Consumer idempotency handles re-processing, but doesn't prevent duplicate writes. Producer deduplication prevents duplicates at the source.

### Design
3. **Design a system to replay a week's worth of Kafka messages for a new consumer that joins late.**
   - Expected: Log retention configuration, consumer offset management, topic compaction tradeoffs, replay at scale.

4. **Your Kafka cluster has 100 partitions across 10 brokers. How would you handle the failure of 3 brokers simultaneously?**
   - Expected: ISR availability, min.insync.replicas, partition leader election, potential unavailability for under-replicated partitions.

### Estimation
5. **LinkedIn processes 7 trillion messages per day through Kafka. Estimate the storage and network bandwidth requirements.**
   - Expected: 7T msgs/day ÷ 86400 = 81M msgs/sec. At 1KB/msg = 81 GB/sec. With replication factor 3 = 243 GB/sec writes. Storage with 7-day retention = ~150 PB.

---

## Chapter 3: URL Shortener

### Conceptual
1. **301 vs 302 redirects: Why might a URL shortener prefer 302 even though it's technically less correct?**
   - Expected: 301 is cached by browsers, making analytics impossible. 302 always hits the server for tracking.

2. **Why is base62 encoding preferred over MD5 truncation for short codes?**
   - Expected: Base62 from sequential IDs is deterministic with no collisions. MD5 truncation risks collisions and doesn't guarantee uniqueness.

### Design
3. **Design a URL shortener that handles 100 billion redirects per year with 99.99% availability.**
   - Expected: CDN edge caching, Redis caching, read replicas, geographic distribution, calculation showing most reads from cache.

4. **How would you add link expiration and custom short codes to a URL shortener?**
   - Expected: TTL in database, background cleanup jobs, custom code collision handling, premium features for custom codes.

### Estimation
5. **Estimate the storage needed for 1 billion unique short URLs with 5-year retention.**
   - Expected: 1B × (7 bytes code + 200 bytes URL + 50 bytes metadata) ≈ 260 GB. With indexes and replication, ~1 TB total.

---

## Chapter 4: WhatsApp

### Conceptual
1. **Why does WhatsApp use Erlang instead of more mainstream languages like Java or Go?**
   - Expected: Actor model, lightweight processes (millions per server), per-process GC (no stop-the-world), hot code reload.

2. **How does the Signal Protocol achieve forward secrecy? Why does this matter for a messaging app?**
   - Expected: Double Ratchet derives new keys after each exchange, old keys are deleted. Compromise of current keys doesn't expose past messages.

### Design
3. **Design the delivery receipt system (✓ sent, ✓✓ delivered, blue ✓✓ read) for WhatsApp.**
   - Expected: ACKs at each stage, async receipt forwarding, handling offline recipients, optimistic UI updates.

4. **How would you implement offline message queuing for a user who hasn't been online for 30 days?**
   - Expected: Per-user queue in durable storage, TTL for messages, batched delivery on reconnect, handling queue overflow.

### Estimation
5. **WhatsApp has 2 billion users and handles 100 billion messages/day. Estimate the server infrastructure needed.**
   - Expected: 100B/day = 1.2M msgs/sec. At 2M connections/server, need 500+ servers for connections alone. Plus storage, routing, etc.

---

## Chapter 5: Slack

### Conceptual
1. **Why does Slack shard by workspace instead of by user or by channel?**
   - Expected: Workspace is the natural isolation boundary for enterprise data. Cross-workspace queries are rare. Compliance requirements.

2. **How does Slack handle search for messages across a workspace with 10 million messages?**
   - Expected: Elasticsearch indexing, real-time index updates, handling freshness vs. latency tradeoffs.

### Design
3. **Design Slack Connect (channels shared between two different companies).**
   - Expected: Message mirroring between shards, identity federation, permission isolation, handling company departures.

4. **How would you implement the "X is typing..." indicator with 100 users in a channel?**
   - Expected: Debounce typing events, aggregate typing status, broadcast efficiently, handle stale indicators.

### Estimation
5. **A large Slack workspace has 50,000 users, 10,000 channels, and 1 million messages/day. Estimate the read load for timeline construction.**
   - Expected: Average user checks 20 channels. 50K users × 20 channels × 10 checks/day = 10M channel reads/day = 115 reads/sec.

---

## Chapter 6: YouTube/Vitess

### Conceptual
1. **What is a VIndex in Vitess, and why is it necessary for sharded databases?**
   - Expected: Secondary index mapping non-shard-keys to shard locations. Without it, queries on non-shard columns hit all shards.

2. **How does Vitess perform online schema changes without downtime?**
   - Expected: Ghost tables, binary log replication of changes, atomic table swap. Similar to gh-ost.

### Design
3. **Design the data model for YouTube video metadata, considering that videos are sharded by channel_id but often queried by video_id.**
   - Expected: Channel-based sharding with VIndex for video_id lookups. Discuss alternatives and tradeoffs.

4. **A shard has grown to 500GB and needs to be split. Walk through the Vitess resharding process.**
   - Expected: Create new shards as replicas, filtered replication, gradual traffic shift, cutover, cleanup.

### Estimation
5. **YouTube has 800 million videos. With 100 million rows per shard as the limit, how many shards are needed?**
   - Expected: 800M / 100M = 8 shards minimum. With safety margin (50% target utilization), 16+ shards. Plus replicas = 50+ MySQL instances.

---

## Chapter 7: Spotify

### Conceptual
1. **Why does Spotify use adaptive bitrate streaming instead of fixed bitrate?**
   - Expected: Network conditions vary. ABR switches quality per chunk to maintain playback. Better UX on unstable connections.

2. **Explain how Spotify's Discover Weekly can recommend songs you've never heard.**
   - Expected: Collaborative filtering (similar users), audio feature analysis, NLP on metadata. Combining multiple signal types.

### Design
3. **Design the offline sync system for Spotify Premium, including what to cache and when.**
   - Expected: Predictive caching based on listening history, opportunistic downloads on WiFi, storage budget management.

4. **How would you design the "Recently Played" feature that syncs across all devices?**
   - Expected: Play event stream, last-write-wins conflict resolution, device sync protocol, handling offline devices.

### Estimation
5. **Estimate Spotify's monthly bandwidth costs for streaming (600M users, 30 hours/month average).**
   - Expected: 600M × 30 hrs × 100MB/hr (average quality) = 1.8 EB/month. At $0.02/GB = $36M/month. CDN caching reduces this significantly.

---

## Chapter 8: Google Docs

### Conceptual
1. **Why did Google Docs choose Operational Transformation (OT) over CRDTs?**
   - Expected: OT was more mature in 2006, works well with central server, simpler memory model. CRDTs are better for peer-to-peer.

2. **Explain the transformation function for two concurrent insert operations at the same position.**
   - Expected: One insert stays at position, other shifts by first's length. Tiebreaker based on user ID ensures determinism.

### Design
3. **Design the offline editing feature for Google Docs, including conflict resolution on reconnect.**
   - Expected: Local operation log, server sync with transformation, handling divergent histories, user-facing conflict UI.

4. **How would you implement cursor synchronization for showing other users' cursor positions?**
   - Expected: Cursor as position index, transform with text operations, broadcast via WebSocket, handling staleness.

### Estimation
5. **A document has 10 concurrent editors, each typing 100 characters/minute. Estimate the operation throughput.**
   - Expected: 10 × 100 = 1000 chars/min = 17 ops/sec (if each character is an operation). With batching (every 200ms), 50 batches/sec.

---

## Chapter 9: Twitter Timeline

### Conceptual
1. **Why does Twitter use a hybrid fan-out approach (fan-out on write for regular users, fan-out on read for celebrities)?**
   - Expected: Fan-out on write is fast to read but celebrities would take minutes to fan out. Hybrid optimizes for both cases.

2. **What happens when a tweet is deleted? How does this affect timelines?**
   - Expected: Soft delete in database, lazy removal from caches, possible brief visibility after deletion.

### Design
3. **Design the "For You" algorithmic timeline that ranks tweets by relevance.**
   - Expected: ML model for ranking, feature engineering (engagement, recency, relationships), A/B testing framework.

4. **How would you handle a celebrity with 100 million followers tweeting during a live event?**
   - Expected: Celebrity tweets merged at read time, caching of popular celebrity feeds, rate limiting fan-out.

### Estimation
5. **Twitter has 500M tweets/day and 400M active users. Estimate the total daily fan-out writes assuming average 200 followers.**
   - Expected: 500M tweets × 200 followers = 100B fan-out writes/day. At 90% non-celebrity (pre-computed), that's 90B writes/day = 1M writes/sec.

---

## Chapter 10: Reddit

### Conceptual
1. **Why does Reddit use eventual consistency for vote counts instead of strong consistency?**
   - Expected: Vote counts change constantly. Exact counts don't matter for ranking. Eventual consistency allows batching and reduces database load.

2. **Explain Reddit's hot ranking algorithm. Why does it use log10 of the vote score?**
   - Expected: Log scale means 10x votes = +1 to score. Prevents runaway posts, makes time more important for sorting.

### Design
3. **Design the "Hot" ranking system for a subreddit with 1 million posts.**
   - Expected: Pre-compute hot scores, sorted sets in Redis, periodic recalculation, handling score changes from votes.

4. **How would you detect and prevent vote manipulation (brigading, bots)?**
   - Expected: Anomaly detection, rate limiting, weighting votes by account age/karma, shadowbans.

### Estimation
5. **r/AskReddit has 40 million subscribers. A popular post gets 50,000 comments. Estimate the read load for displaying comment threads.**
   - Expected: Assume 1% daily active = 400K users. If 10% view the post = 40K. Each loads comments = 40K reads. Plus pagination and sorting.

---

## Chapters 11-18

*(Following the same pattern: 2 conceptual, 2 design, 1 estimation per chapter)*

### Chapter 11: Bluesky - Focus on DIDs, AT Protocol, feed generators
### Chapter 12: Tinder - Focus on geospatial indexing, ELO scoring, bloom filters
### Chapter 13: Uber - Focus on H3, real-time matching, ETA calculation
### Chapter 14: Stock Exchange - Focus on matching engines, latency, sequencing
### Chapter 15: Stripe - Focus on idempotency, distributed transactions, reconciliation
### Chapter 16: ChatGPT - Focus on GPU batching, KV cache, token streaming
### Chapter 17: Meta Serverless - Focus on cold start, scheduling, container management
### Chapter 18: Scaling - Focus on progressive architecture, when to add components

---

## Interview Tips

### Before the Interview
- Clarify requirements: Scale, latency, consistency needs
- Ask about constraints: Budget, timeline, existing systems
- State assumptions explicitly

### During the Design
- Start with high-level architecture
- Identify the core challenges
- Deep dive on 2-3 interesting components
- Discuss tradeoffs at each decision point

### Estimation Approach
1. Clarify what you're estimating
2. Start from user-facing numbers (DAU, requests)
3. Work through the system (read/write ratio, fanout)
4. Sanity check against known systems
5. Discuss sensitivity to assumptions
