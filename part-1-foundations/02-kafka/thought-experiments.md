# Thought Experiments: Apache Kafka

These exercises test deep understanding of Kafka's design principles.

---

## Experiment 1: The Ordering Guarantee

**Scenario:**
Your e-commerce system uses Kafka to process order events. The events are:
1. `order_created`
2. `payment_received`
3. `order_shipped`

**Questions to consider:**

1. A consumer receives events in this order: `payment_received`, `order_created`, `order_shipped`. How is this possible? What did the producer do wrong?

2. You have 10 partitions for the orders topic. Customer A places 2 orders. Can events for Order 1 and Order 2 be processed out of order relative to each other? Should you care?

3. You want strict ordering for all events from the same customer (not just same order). How would you partition? What's the scalability risk?

4. A hot seller runs a flash sale—10,000 orders in 1 minute, all for the same product. If you partition by `product_id`, what happens? How would you handle this "hot partition" problem?

**The insight:** Kafka guarantees ordering within a partition, not across partitions. Your partition key choice determines your ordering semantics.

---

## Experiment 2: The Retention Dilemma

**Scenario:**
Your Kafka cluster has 100 TB of storage. You're currently at 80 TB with 7 days of retention. Marketing wants to run analytics on 30 days of data.

**Questions to consider:**

1. Increasing retention to 30 days would require ~400 TB. That's a 4x infrastructure cost increase. What alternatives exist?

2. If you move old data to S3 (tiered storage), what happens when an analytics job needs to read from day 15? What's the latency impact?

3. A developer suggests: "Let's just re-publish important events to a long-retention topic." What are the consistency risks?

4. Log compaction keeps the latest value per key forever. Could you use compaction for the analytics use case? What data would you lose?

**The insight:** Retention is a tradeoff between infrastructure cost, query latency, and data completeness. There's no free lunch.

---

## Experiment 3: The Exactly-Once Cost

**Scenario:**
You're building a payment processing system. Duplicates are unacceptable. You enable exactly-once semantics (EOS).

**Questions to consider:**

1. EOS adds ~20% latency overhead. For 10 million payments/day with P99 latency increasing from 50ms to 60ms, is this acceptable? What's the business impact of a duplicate payment vs. 10ms extra latency?

2. A developer points out: "We could just deduplicate in the database with a unique constraint on payment_id." Compare this approach to Kafka's EOS. What are the tradeoffs?

3. You have exactly-once within Kafka, but what happens when you write the result to an external database? The database write could fail after Kafka committed. How do you achieve end-to-end exactly-once?

4. Your producer crashes mid-transaction. What happens to the partial messages? How long until they're either committed or aborted?

**The insight:** Exactly-once within Kafka is well-defined. End-to-end exactly-once requires careful application design (idempotent consumers, outbox pattern, etc.).

---

## Experiment 4: The Rebalancing Storm

**Scenario:**
Your consumer group has 20 consumers processing 100 partitions. Deployment happens daily, restarting all consumers.

**Questions to consider:**

1. If all 20 consumers restart simultaneously, how many rebalances occur? What's the processing downtime?

2. You implement rolling restarts (one consumer at a time, 30-second delay). With default `session.timeout.ms=10s` and `heartbeat.interval.ms=3s`, what happens?

3. A consumer has a bug causing it to crash-loop (start, crash, restart every 5 seconds). What's the blast radius on the other 19 healthy consumers?

4. Kafka added "static membership" where consumers have stable IDs and short restarts don't trigger rebalance. What's the downside? (Hint: Think about what happens if a consumer truly dies vs. just restarts.)

**The insight:** Consumer group rebalancing is a distributed consensus problem. Optimizing for fast failure detection conflicts with avoiding unnecessary rebalances.

---

## Experiment 5: The Multi-Datacenter Challenge

**Scenario:**
You have Kafka clusters in US-East and EU-West. Events need to be in both regions within 5 seconds for compliance.

**Questions to consider:**

1. MirrorMaker 2 replicates topics between clusters. If US-East produces 10,000 messages/second, and network latency to EU-West is 80ms, what's the replication lag lower bound?

2. An event is produced in US-East, replicated to EU-West. A consumer in EU-West reads it and produces a response. That response is replicated back to US-East. A US-East consumer reads the original and the response—but in what order?

3. If US-East goes completely offline, EU-West consumers continue processing. When US-East comes back, there's a "split-brain" period where both clusters accepted writes. How do you reconcile?

4. An architect proposes: "Let's use a single global Kafka cluster stretched across both regions." What would be the latency for a write that requires acks=all?

**The insight:** Multi-region Kafka is fundamentally about choosing between availability and consistency. Most real systems choose availability and design applications to handle eventual consistency.
