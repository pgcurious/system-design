# Thought Experiments: Amazon S3

These exercises test deep understanding of S3's design principles.

---

## Experiment 1: The Replication Dilemma

**Scenario:**
You're designing a new S3 storage class called "S3 Budget" with 99.99% durability (4 nines instead of 11). The goal is to reduce costs by 60%.

**Questions to consider:**

1. If S3 Standard uses 3-way replication across 3 AZs, how many replicas would you use for S3 Budget? Would you keep them in the same AZ or across AZs?

2. Calculate the expected object loss rate for your design. If a customer stores 1 million objects for 1 year, how many might they lose?

3. Who would actually use this storage class? What types of data would be acceptable to store here?

4. Amazon actually created "S3 One Zone-IA" which stores data in a single AZ. How does this compare to your design? What failure mode does it accept?

**The insight:** Durability is a spectrum, and different data has different value. Log files from last month are not worth the same protection as customer financial records.

---

## Experiment 2: The Metadata Bottleneck

**Scenario:**
Every S3 operation requires a metadata lookup: "Where is this object stored?" The metadata service is now the bottleneck.

**Questions to consider:**

1. If S3 serves 100 million requests/second globally and each metadata lookup takes 2ms, how many metadata servers would you need? What assumptions are you making?

2. Could you cache metadata? What's the invalidation challenge when an object is deleted or overwritten?

3. What if metadata itself was stored on... S3? (This was actually the case and caused the 2017 outage.) How would you break this dependency?

4. DynamoDB was born from Amazon's need to solve metadata at scale. What properties of a key-value store make it suitable for this use case?

**The insight:** At S3's scale, the metadata problem is often harder than the data storage problem.

---

## Experiment 3: The Eventual Consistency Era

**Scenario:**
Before December 2020, S3 had eventual consistency for overwrites. This meant:

```
PUT object (version 2)
GET object → might return version 1
... wait ...
GET object → returns version 2
```

**Questions to consider:**

1. Why did S3 originally choose eventual consistency? What engineering complexity does it avoid?

2. Give an example of an application that would break under eventual consistency. Now give an example of an application where it doesn't matter.

3. In December 2020, S3 announced strong read-after-write consistency. What changed in their architecture to enable this? (Hint: It required a new versioning system.)

4. The announcement claimed this was achieved "at no additional cost." How might this be possible? What optimizations might they have made?

**The insight:** Consistency guarantees are not binary. The same system can evolve its guarantees as infrastructure improves.

---

## Experiment 4: Cost Engineering

**Scenario:**
Your company stores 1 PB of data on S3 Standard. Monthly cost: ~$23,000. The CFO asks you to reduce this by 50%.

**Questions to consider:**

1. You analyze access patterns and find:
   - 10% of data accessed daily (hot)
   - 30% accessed monthly (warm)
   - 60% accessed less than once per year (cold)

   Design a tiering strategy using S3 storage classes. Calculate the new monthly cost.

2. There's a catch: Moving data between tiers has a cost. If warm data is occasionally accessed, moving it to Glacier and retrieving it might cost more than keeping it on Standard. What's the break-even point?

3. S3 Intelligent Tiering claims to automate this. Why wouldn't everyone use it? (Hint: Look at the monitoring fee per object.)

4. A colleague suggests compressing all data before uploading. What are the tradeoffs? When does compression make economic sense?

**The insight:** Cloud storage pricing is a multidimensional optimization problem. Access patterns, retrieval needs, and data characteristics all matter.

---

## Experiment 5: The Disaster Recovery Test

**Scenario:**
Your company uses S3 for critical backups. The CISO asks: "What's our recovery time if AWS loses an entire region?"

**Questions to consider:**

1. If you have cross-region replication enabled, what data might be lost during a sudden region failure? (Think about replication lag.)

2. You have 100 TB of data in us-east-1 with cross-region replication to us-west-2. Region fails at 2 AM. Walk through the recovery steps. How long until applications are running again?

3. A developer suggests: "Let's just read from us-west-2 as a backup automatically." What could go wrong if us-east-1 is not actually down, just slow?

4. Design a multi-region active-active architecture for S3. What consistency challenges arise when writes can happen in any region?

**The insight:** Disaster recovery is not just about data survival—it's about recovery time, consistency during failover, and avoiding split-brain scenarios.
