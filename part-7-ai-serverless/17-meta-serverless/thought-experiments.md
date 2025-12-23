# Thought Experiments: Meta Serverless

These exercises test deep understanding of serverless platforms, container orchestration, and resource management at scale.

---

## Experiment 1: The Cold Start Trade-off

**Scenario:**
You can eliminate cold starts entirely by keeping one warm container per function at all times. But you have 500,000 registered functions and only 10,000 hosts with 256 container slots each.

**Questions to consider:**

1. You can keep 2.56 million containers warm, but have 500,000 functions. How do you decide which functions get pre-warmed containers? What signals indicate a function will be invoked soon?

2. A function hasn't been called in 30 days. Keeping a warm container costs $0.01/day. A cold start costs the user 500ms. At what invocation frequency does pre-warming pay off?

3. Predictive warming uses ML to forecast invocations. Your predictions are 80% accurate. What's the cost of false positives (warming a function that isn't called) vs. false negatives (cold start for a function you should have warmed)?

4. Functions often arrive in bursts (cron jobs at 00:00 UTC, mobile app usage at 9am). How would you handle predictable bursts differently from random arrivals?

**The insight:** Cold start elimination is an optimization problem balancing memory cost against latency cost. The optimal strategy depends on invocation patterns, which vary dramatically across functions.

---

## Experiment 2: The Noisy Neighbor

**Scenario:**
Function A is CPU-intensive (video transcoding). It's co-located with Function B (a latency-sensitive API handler). Despite cgroup CPU limits, Function B's p99 latency spikes from 10ms to 200ms whenever Function A is running.

**Questions to consider:**

1. cgroups limit CPU time over a period (e.g., 50% = 50ms per 100ms window). But within that window, bursts can still cause cache evictions and memory bus contention. How would you detect and prevent this?

2. Moving to CPU cores instead of CPU shares (pinning Function A to cores 0-3, Function B to cores 4-7) eliminates the problem. But it reduces utilization. When is this tradeoff worthwhile?

3. You could move latency-sensitive functions to dedicated hosts. But how do you know which functions are latency-sensitive? The developer might not tell you.

4. Intel's Cache Allocation Technology (CAT) lets you partition L3 cache between workloads. Is this worth the complexity? What percentage of noisy neighbor issues are cache-related vs. other contention?

**The insight:** Resource isolation is harder than it looks. cgroups provide guarantees for CPU time and memory, but shared resources (cache, memory bus, network) create subtle interference patterns that are hard to prevent.

---

## Experiment 3: The Burst Event

**Scenario:**
It's Super Bowl Sunday. Your notification service normally runs 1,000 invocations/second. At the end of the game, it spikes to 100,000 invocations/second for 60 seconds as millions of users get score updates.

**Questions to consider:**

1. Your container pool can handle 10,000 invocations/second with warm containers. Where do you get capacity for the other 90,000/second? Can cold starts be fast enough?

2. You could queue the burst and process over 10 minutes instead of 1 minute. But notifications lose value with delay. How do you decide which notifications to delay?

3. Over-provisioning for Super Bowl (10x normal capacity) costs $1M/year in idle resources. Under-provisioning loses customers. What's the right balance?

4. Events like the Super Bowl are predictable. How would you build a system that pre-provisions for known events? What about unpredictable events (celebrity tweet, breaking news)?

**The insight:** Bursty workloads break the efficiency model of serverless. The whole point of serverless is paying only for what you use. But handling bursts requires capacity that sits idle most of the time.

---

## Experiment 4: The Long-Running Function

**Scenario:**
Your platform has a 30-second timeout. A data science team wants to run a function that trains a small ML model, which takes 5 minutes. They argue: "It's still stateless! It's serverless!"

**Questions to consider:**

1. Why do serverless platforms have timeouts? What breaks if you allow 5-minute functions? (Hint: think about scheduling, billing, and container utilization)

2. Could you implement a "step function" pattern—the long job checkpoints every 30 seconds, and the platform resumes it? What's the overhead of checkpointing ML training?

3. Long-running jobs affect bin packing—a 5-minute job holds a container slot 10x longer than a 30-second job. How does this impact other users on the same host?

4. Alternative: Run long jobs on dedicated "batch" infrastructure separate from the main serverless platform. When does this make sense? When does it break the serverless abstraction?

**The insight:** Serverless abstractions work well for short, stateless functions. Long-running, stateful jobs strain the model. The question is whether to extend serverless or provide a different abstraction for different workloads.

---

## Experiment 5: The Stateful Function

**Scenario:**
A game studio uses your platform for game backend. Their functions need to:
1. Track player session state (last action, current room)
2. Access shared game world state (who's online, world events)
3. Handle 100ms latency requirement for responsive gameplay

**Questions to consider:**

1. Serverless functions are stateless by design. The session state must live somewhere. Options: external Redis, local cache with sticky routing, function instance memory. What are the tradeoffs?

2. Sticky routing (same player → same container) keeps state local but breaks load balancing. How do you handle when that container crashes? When the player goes idle?

3. The shared world state is read by every request. Should it be pushed to all containers proactively, or pulled on demand? What's the consistency model?

4. Some games use "virtual servers"—a container pretends to be a dedicated server for a game room. This breaks serverless assumptions. When is this pattern worth it?

**The insight:** State is the enemy of scalability, but many applications fundamentally need state. The art is finding the right place to put state that balances latency, consistency, and failure recovery.

---

## Bonus: Design Exercise

**Design a serverless scheduler that handles the following:**
1. 11.5 million invocations/second
2. P99 cold start < 200ms
3. 500,000 registered functions
4. Predictive warming with ML model
5. Fair scheduling across teams/organizations

**Sketch the architecture and key algorithms.**

Considerations:
- How do you scale the scheduler itself? Is it centralized or distributed?
- What state does the scheduler maintain? Where is it stored?
- How do you handle scheduler failure? What happens to in-flight requests?
- What metrics do you track to improve scheduling decisions?
