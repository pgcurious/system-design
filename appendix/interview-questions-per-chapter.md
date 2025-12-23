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

## Chapter 11: Bluesky

### Conceptual
1. **Why does Bluesky use DIDs (Decentralized Identifiers) instead of traditional usernames for identity?**
   - Expected: DIDs are cryptographic identifiers that users own. If a server bans you or shuts down, your DID persists. Usernames (handles) are just human-readable aliases pointing to DIDs.

2. **How do feed generators enable algorithm choice, and why is this architecturally significant?**
   - Expected: Feed generators are external services that query the public firehose and apply custom ranking. Users subscribe to feeds they trust. This separates content curation from the platform itself.

### Design
3. **Design the relay/firehose system that aggregates events from all Personal Data Servers (PDSs).**
   - Expected: Fan-in from PDSs, signature validation, event ordering, fan-out to App Views. Discuss scaling (sharding by PDS), backpressure handling, and catchup for disconnected consumers.

4. **How would you implement portable identity so users can migrate their account from one PDS to another without losing followers?**
   - Expected: DID document contains current PDS endpoint. Migration updates the DID document. Followers follow the DID, not the server. Discuss cryptographic handoff and data transfer.

### Estimation
5. **Bluesky has 10 million users, averaging 100 events/user/day. Estimate the relay throughput requirements.**
   - Expected: 10M × 100 = 1 billion events/day = ~11,600 events/sec. At 1KB/event = ~11 MB/sec. With App View fan-out to 10 consumers = 110 MB/sec. Manageable for a small cluster.

---

## Chapter 12: Tinder

### Conceptual
1. **Why does Tinder use bloom filters for tracking swiped users instead of a database query with NOT IN clause?**
   - Expected: With 10,000+ swipes per user, NOT IN is O(n). Bloom filters give O(1) lookup with small false positive rate (acceptable: skip showing someone unnecessarily) and no false negatives (never show already-swiped).

2. **Explain how ELO-style scoring creates a balanced matching ecosystem. What problems does it solve?**
   - Expected: Without ELO, popular users get overwhelmed, unpopular users get no matches. ELO shows high-desirability users to each other first. System learns desirability from swipe behavior.

### Design
3. **Design the recommendation system that selects which profiles to show and in what order.**
   - Expected: H3 cell query for nearby users, bloom filter to exclude swiped, preference filtering (age, gender), ELO/ML ranking. Discuss batch generation vs. real-time, caching, and handling the SuperLike priority queue.

4. **How would you implement real-time match detection (both users swiped right) at 11,500 swipes/second?**
   - Expected: On each right-swipe, check if reverse swipe exists. Index by (target_user, source_user) for O(1) lookup. Async notification pipeline. Handle the celebrity problem (batched notifications).

### Estimation
5. **Estimate the storage needed for bloom filters if 10 million users each have 10,000 swipes.**
   - Expected: Bloom filter for 10K entries with 1% false positive ≈ 12KB. 10M users × 12KB = 120 GB. Fits in a Redis cluster. Plus swipe events: 10M × 10K × 20 bytes = 2 TB in persistent storage.

---

## Chapter 13: Uber

### Conceptual
1. **Why does Uber match on ETA (estimated time of arrival) rather than straight-line distance?**
   - Expected: A driver 2 miles away across a river might be 15 minutes out. A driver 5 miles away on the same road might be 3 minutes. ETA accounts for roads, traffic, and routing. Distance is misleading.

2. **Explain how H3 hexagonal indexing improves upon geohash for location queries.**
   - Expected: Hexagons have 6 equidistant neighbors (consistent distances). Squares have 8 neighbors with varying distances. No edge discontinuity problem. Hierarchical resolution for different use cases.

### Design
3. **Design the location update system that handles 10+ million GPS updates per second.**
   - Expected: Ring buffers (ephemeral, no disk write), H3 index updates only on cell boundary crossing (~10% of updates), sharding by H3 cell, eventual consistency acceptable for location data.

4. **How would you implement batched dispatch matching to optimize global wait times rather than greedy per-ride matching?**
   - Expected: Collect requests in 2-second windows, collect available drivers, solve assignment problem (Hungarian algorithm or approximation), batch offers. Discuss latency tradeoff vs. efficiency gain.

### Estimation
5. **5 million active drivers updating location every 4 seconds. Estimate the memory and bandwidth requirements.**
   - Expected: 5M ÷ 4 = 1.25M updates/sec. At 50 bytes/update = 62.5 MB/sec inbound. Per-driver state: 800 bytes × 5M = 4 GB total. H3 index updates: ~125K/sec (10% cross cell boundaries).

---

## Chapter 14: Stock Exchange

### Conceptual
1. **Why do exchanges use a sequencer to assign order numbers rather than relying on timestamps?**
   - Expected: At nanosecond scales, multiple orders arrive "simultaneously." Clocks drift. Sequencer creates a single, canonical, auditable order. Deterministic processing requires deterministic input ordering.

2. **Explain why kernel bypass networking (DPDK, Solarflare) is essential for modern exchanges.**
   - Expected: Normal path: NIC → Kernel → User space = ~10μs. Bypass path: NIC → User space = ~1μs. When competitors measure latency in microseconds, 9μs advantage is worth millions.

### Design
3. **Design an order book data structure that supports O(1) best bid/ask lookup and efficient order matching.**
   - Expected: Two sorted maps (buy side descending by price, sell side ascending). At each price level, FIFO queue of orders. Best bid/ask at top. Matching walks from top until order filled.

4. **How would you implement hot standby failover for a matching engine with <10 second recovery?**
   - Expected: Secondary processes same sequenced events, maintains identical order book. Heartbeat failure detection. Atomic switchover. Journal replay to catch up if needed. Discuss state synchronization.

### Estimation
5. **An exchange processes 500,000 orders/second with 5 messages per order. Estimate the market data multicast bandwidth.**
   - Expected: 500K × 5 = 2.5M messages/sec. At 100 bytes/message = 250 MB/sec. Unicast to 10,000 subscribers would be 2.5 TB/sec. Multicast: still 250 MB/sec. Hence multicast is essential.

---

## Chapter 15: Stripe

### Conceptual
1. **Why must idempotency keys be generated by the client rather than the server?**
   - Expected: If server generates key and network fails before client receives it, client retries → new key → duplicate charge. Client-generated key survives network failures because client uses same key on retry.

2. **What's the difference between idempotency and exactly-once semantics? Why does Stripe need reconciliation?**
   - Expected: Idempotency prevents duplicate processing of retries. But partial failures can still occur (card charged, database write failed). Reconciliation catches edge cases: compare card network records with database, create/refund as needed.

### Design
3. **Design the idempotency cache system handling 1 billion requests/day with concurrent retry detection.**
   - Expected: Redis cluster, key → (status, response), 24h TTL. Critical: handle in-flight requests with distributed locking. If status=PROCESSING, wait and poll. Fallback to database if cache fails.

4. **How would you implement the distributed transaction for: charge card → save record → return response?**
   - Expected: Not a traditional 2PC (can't coordinate with card networks). Instead: charge first, then save (can reconcile orphaned charges). Pending queue for uncertain states. Background reconciliation job.

### Estimation
5. **1 billion requests/day with 1KB average cache entry. Estimate the idempotency cache storage requirement.**
   - Expected: 1B × 1KB = 1TB daily. With 24h TTL, max 1TB at any time. Redis cluster: 10 nodes × 100GB = 1TB. Add 50% headroom = 15 nodes. Cache hit rate for retries: ~5% of requests are retries.

---

## Chapters 16-18

*(Coming soon: Following the same pattern as above)*

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
