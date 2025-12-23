# Thought Experiments: YouTube & Vitess

These exercises test deep understanding of Vitess and database sharding design.

---

## Experiment 1: The View Counter Problem

**Scenario:**
A video goes viral: 1 million views per hour. That's 278 view increments per second, all hitting the same row.

**Questions to consider:**

1. If you shard by channel_id and this video belongs to a small creator, their entire channel is on one shard. How do you prevent that shard from melting?

2. Incrementing a counter in MySQL: `UPDATE videos SET views = views + 1 WHERE video_id = ?`. What's the row locking implication? What if 100 concurrent increments happen?

3. A classic solution: Batch updates (write to Redis, flush to MySQL every 60 seconds). What's the tradeoff? What view count does the user see during those 60 seconds?

4. YouTube shows "1.2M views" not "1,234,567 views". How does approximate display affect the technical requirements?

**The insight:** High-frequency updates to a single row is a distributed systems anti-pattern. The solution is often not database optimization but rethinking the data model.

---

## Experiment 2: The Comment Section

**Scenario:**
A video has 500,000 comments. Users want to see "newest first" and "top comments." Moderators want to see "reported comments."

**Questions to consider:**

1. Comments are related to videos. If videos are sharded by channel_id, where do comments live? Same shard as the video? Separate comment shards?

2. "Top comments" requires sorting by (upvotes - downvotes). This changes constantly. Do you re-sort on every request, or pre-compute and accept staleness?

3. A user blocks another user. That user's comments should be hidden. If you have 500K comments, how do you efficiently filter out blocked users without loading all comments?

4. Legal takedown: Remove a comment globally within 24 hours. How does this propagate across shards and caches?

**The insight:** Comments look simple but combine read-heavy access patterns (sorting, pagination) with write-heavy patterns (votes, moderation) and complex filtering (blocks, reports).

---

## Experiment 3: The Channel Subscription Table

**Scenario:**
MrBeast has 300 million subscribers. Each subscription is a row: (user_id, channel_id, subscribed_at).

**Questions to consider:**

1. If sharded by channel_id, MrBeast's shard has 300 million rows just for subscriptions. If sharded by user_id, "get all subscribers of channel X" hits every shard. What's the solution?

2. A user subscribes to a channel. Write to which shard? Read "am I subscribed to channel X?" from which shard?

3. YouTube needs to notify 300 million users when MrBeast uploads. Even at 100,000 notifications/second, that's 50 minutes. How do you design this at infrastructure level?

4. A user has 500 subscriptions. Show their subscription list. If subscriptions are sharded by user_id, this is fast. But if sharded by channel_id, this scatters to potentially 500 shards. How do you optimize?

**The insight:** Many-to-many relationships in sharded databases are fundamentally hard. You often need to store data twice (sharded by both keys) and keep them synchronized.

---

## Experiment 4: The Resharding Migration

**Scenario:**
You started with 10 shards. You now need 40 shards. You have 100 TB of data across the original shards.

**Questions to consider:**

1. Vitess resharding uses filtered replication. Explain how shard 1 (hash 0-999) becomes shard 1a (hash 0-249), 1b (250-499), 1c (500-749), 1d (750-999) without downtime.

2. During the migration, writes go to new shards but reads might go to old shards. How do you ensure consistency? What if a user writes then immediately reads?

3. Resharding at 100 TB. Estimate how long the data copy phase takes. What's the network bandwidth required? Can you do this without impacting production traffic?

4. A bug in the resharding process: Some rows got duplicated in two shards. How do you detect this? How do you fix it without another resharding?

**The insight:** Resharding is a bet-the-company operation. It must work perfectly, online, without data loss or corruption.

---

## Experiment 5: The Cross-Shard Transaction

**Scenario:**
User action: "Move video X from channel A to channel B." This involves:
- Update video's channel_id (changes which shard it belongs to!)
- Update channel A's video count (-1)
- Update channel B's video count (+1)
- Update VIndex (video_id → new channel_id)

**Questions to consider:**

1. These updates touch 3+ shards. How do you make this atomic? What if the update to shard 2 fails after shard 1 succeeded?

2. Vitess supports two-phase commit (2PC) for cross-shard transactions. What are the performance and availability tradeoffs?

3. An alternative: Use Saga pattern. Perform each update, and compensate on failure. How would you design the compensation logic for the "move video" case?

4. The 2PC coordinator is a single point of failure. What happens if the coordinator crashes mid-transaction? How does Vitess handle this?

**The insight:** Cross-shard transactions are expensive and fragile. The best architecture minimizes them through careful schema design.
