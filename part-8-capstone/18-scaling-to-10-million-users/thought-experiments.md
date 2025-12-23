# Thought Experiments: Scaling to 10 Million Users

These exercises test judgment about when to scale, what to optimize, and how to make architectural decisions under uncertainty.

---

## Experiment 1: The Premature Optimization Trap

**Scenario:**
You have 1,000 users and a single PostgreSQL instance. Your CTO (with Google experience) insists: "Let's shard the database now. I've seen the pain of migrating later. Trust me."

**Questions to consider:**

1. Sharding at 1,000 users means: distributed transactions, cross-shard queries, operational complexity. Your team has 3 engineers. Can you maintain this? What's the cost of sharding too early?

2. Counter-argument: Some decisions are hard to reverse. Auth architecture, database schema, API design. For these, isn't it better to get it right early?

3. How would you distinguish "essential early decisions" from "premature optimization"? What's your framework?

4. Your CTO has valid experience—at Google, not sharding early was a multi-year painful migration. But you're not Google. How do you balance expertise with context?

**The insight:** Past experience at big companies can be both a blessing and a curse. The systems that worked at 100M users might be overkill at 10K users. Context matters more than best practices.

---

## Experiment 2: The Database Decision

**Scenario:**
You're at 100,000 users. PostgreSQL is struggling:
- CPU at 80%
- 500ms p99 query latency
- Read replicas are at capacity

You have three options:
1. Vertical scaling: Move to a bigger instance (4x cost)
2. Horizontal scaling: Add more read replicas + caching
3. Migration: Move to a NoSQL database (DynamoDB)

**Questions to consider:**

1. Vertical scaling is the fastest fix. But you'll hit the ceiling again. At what point is vertical scaling no longer viable? (Hint: What's the biggest PostgreSQL instance available?)

2. Read replicas help with read load but not write load. What's your read/write ratio? If it's 95% reads, replicas might buy you 10x headroom. If it's 50/50, you're in trouble.

3. DynamoDB promises infinite scale, but requires rearchitecting your queries. Some queries (joins, aggregations) don't work. What queries does your app rely on?

4. Your users don't care about your database. They care about latency and features. How do you make this decision without a 3-month project? What's the "minimum viable migration"?

**The insight:** Database scaling is rarely a pure technical decision. It's constrained by team skills, existing queries, timeline, and risk tolerance. The "best" database is the one your team can operate.

---

## Experiment 3: The Microservices Moment

**Scenario:**
Your monolith has grown to 500,000 lines of code. Deploy takes 45 minutes. Three teams step on each other's changes. The VP of Engineering says: "Let's move to microservices."

**Questions to consider:**

1. What's actually broken? Is it the monolith, or is it missing code ownership, poor testing, or inadequate CI/CD? Microservices won't fix a dysfunctional team.

2. If you do extract services, which one first? Options:
   - Auth (high security needs)
   - Notifications (clearly bounded, different scaling)
   - The feature your fastest team owns
   What are the criteria?

3. Microservices create new problems: service discovery, distributed tracing, network latency, deployment coordination. Do you have the platform to support this?

4. Alternative: "Modular monolith"—keep the single deployment but enforce strict boundaries between modules. When is this sufficient? When is it not?

**The insight:** Microservices solve organizational problems, not technical problems. If your org structure doesn't match service boundaries, microservices make things worse, not better.

---

## Experiment 4: The Global Expansion

**Scenario:**
You're US-only with 1 million users. Product launches in Europe. EU users report 300ms latency (vs. 50ms in US). They're churning. The CEO says: "Fix Europe."

**Questions to consider:**

1. What's causing the 300ms? Options:
   - Physics (speed of light: ~100ms round trip US to EU)
   - Overloaded US infrastructure
   - No CDN caching in EU
   Which do you investigate first?

2. Adding a CDN might cut latency to 150ms for static content. Is that enough? What if 80% of requests are API calls that must hit the origin?

3. Full multi-region (EU replica) is a 6-month project. Your EU users are churning now. What's the 2-week fix that buys you time?

4. GDPR requires EU user data to stay in EU. This isn't just about latency—it's about compliance. How does this change your architecture?

**The insight:** Global expansion is multi-dimensional: latency, compliance, cost, complexity. You can't just "add a region." You need to rethink data flow, privacy, and operational readiness.

---

## Experiment 5: The Cost Cliff

**Scenario:**
At 5 million users, your cloud bill is $500K/month—60% of revenue. Investors are concerned about unit economics. Your options:
1. Negotiate cloud discounts (committed use)
2. Optimize code and infrastructure
3. Move workloads to reserved instances or bare metal
4. Raise prices

**Questions to consider:**

1. Which workloads are driving cost? Compute, storage, bandwidth, managed services? You need visibility before optimization. Do you have cost attribution per feature/team?

2. Committed use discounts (1-year, 3-year) can cut costs 40%. But you're betting on growth. What if users churn? What if you need to change regions?

3. Moving from managed services (RDS, Lambda) to self-managed (EC2 + PostgreSQL) cuts cost but adds ops burden. Your team is 30 engineers. Do you have capacity for ops?

4. Raising prices is a product decision, not an engineering decision. But engineering can enable better pricing (metering per feature, tiered access). What would you build?

**The insight:** Cloud economics change as you scale. The "pay for what you use" model is great at small scale. At large scale, committed use, reserved instances, and even on-prem become attractive.

---

## Bonus: Decision Framework Exercise

**Build a decision tree for: "Should we add a new infrastructure component?"**

Starting question: "Is the current system at capacity?"

For each branch, define:
- What evidence you need
- What the next question is
- When you've reached a decision

Example branches:
- Current system at capacity → Yes → Is it optimizable without new components?
- Current system at capacity → No → Will it be at capacity in 6 months?

**The goal is a repeatable framework your team can use for any scaling decision.**
