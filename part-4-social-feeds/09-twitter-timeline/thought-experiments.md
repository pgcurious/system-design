# Thought Experiments: Twitter Timeline

These exercises test deep understanding of social feed design at scale.

---

## Experiment 1: The Following Explosion

**Scenario:**
A new user follows 10,000 accounts on their first day (an aggressive bot, or someone importing follows from another platform).

**Questions to consider:**

1. If timelines are precomputed via fan-out on write, this user's timeline was empty before. How do you bootstrap it with 10,000 accounts' worth of tweets?

2. Rate-limiting follows (e.g., max 100/day) is one solution. What are the UX implications? How do legitimate users (e.g., joining a new company and following all coworkers) get affected?

3. If you allow bulk follows, the timeline hydration is expensive. Should this user wait 10 minutes for their timeline to populate, or see a degraded experience immediately?

4. The reverse: An account gets 1 million new followers in an hour (celebrity drama). How does this affect their tweet delivery to new followers?

**The insight:** Fan-out on write assumes gradual graph changes. Sudden graph changes break the model.

---

## Experiment 2: The Retweet Amplification

**Scenario:**
@alice tweets. @bob (10M followers) retweets it. Now @alice's tweet should appear in 10 million more timelines.

**Questions to consider:**

1. Is a retweet stored as a new object, or a reference to the original tweet? What are the tradeoffs?

2. If @alice deletes the original tweet, what happens to @bob's retweet? What should the 10 million followers see?

3. If @alice blocks @bob, should @bob's retweet still show to @bob's followers? What about @alice's original tweet embedded in the retweet?

4. Engagement counting: The original tweet shows "1M retweets." But each retweet has its own engagement (likes on the retweet, not the original). How do you attribute engagement?

**The insight:** Retweets are references with independent lifecycle. The fan-out implications compound: 1 retweet by a celebrity = 10M more fan-out operations.

---

## Experiment 3: The Real-Time Event

**Scenario:**
The Super Bowl is happening. 20 million users are actively tweeting and refreshing timelines.

**Questions to consider:**

1. Fan-out on write has a queue. During the Super Bowl, the queue grows to 1 billion pending writes. What's the strategy?

2. "Trending" topics show real-time counts. How do you count "tweets about Super Bowl in the last minute" when there are 100,000+ per minute?

3. Users expect to see reactions instantly (within seconds). But your infrastructure is struggling. How do you prioritize?
   - Verified accounts' tweets?
   - High-engagement tweets?
   - Tweets from people you interact with most?

4. After the event, you have a 30-minute fan-out backlog to process. Should you process it all (users see stale tweets), or discard old operations (users miss tweets)?

**The insight:** Real-time events create temporal hotspots. The system must degrade gracefully rather than fail completely.

---

## Experiment 4: The Algorithm Tuning

**Scenario:**
Twitter's ranking algorithm decides what users see. A small change can shift millions of impressions.

**Questions to consider:**

1. You change the ranking weight from "engagement" to "recency." This shifts 10% of impressions from high-engagement to recent tweets. How do you measure impact?

2. An A/B test: 50% of users see Algorithm A, 50% see Algorithm B. A creator has followers in both groups. Their tweet performance is inconsistent. Is this fair to creators?

3. The algorithm optimizes for "time spent." This might favor outrage content. How do you balance engagement metrics with "quality" or "healthiness" of content?

4. Users want control: "Show me chronological timeline." But the ranked timeline has better engagement. How do you balance user control with business metrics?

**The insight:** The timeline algorithm is a policy decision with business, ethical, and technical dimensions.

---

## Experiment 5: The Distributed Timeline

**Scenario:**
Twitter expands to multiple regions. Data centers in US, EU, and Asia. User timelines are in the nearest region.

**Questions to consider:**

1. @alice is in US, @bob is in EU. @alice follows @bob. When @bob tweets, where does the fan-out happen? How does the tweet reach @alice's US-based timeline?

2. Cross-region replication has 100ms latency. A viral tweet spreads globally. The US sees it 100ms before EU. Is this acceptable?

3. A user travels from US to EU. Their timeline data is in US region. Do you:
   - Redirect reads to US (200ms latency)?
   - Migrate timeline to EU (complex, slow)?
   - Serve stale data while syncing?

4. Fan-out queues are per-region. A celebrity in US tweets to 50M followers globally. How do you distribute the fan-out work across regions?

**The insight:** Multi-region adds latency and consistency tradeoffs to an already complex system.
