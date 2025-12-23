# Thought Experiments: Reddit

These exercises test deep understanding of ranking and voting systems.

---

## Experiment 1: The Hot Algorithm Parameters

**Scenario:**
Reddit's hot algorithm: `sign * log10(score) + seconds / 45000`

The "45000" constant means: Every 12.5 hours, a post needs 10x more votes to maintain the same ranking.

**Questions to consider:**

1. What if we changed 45000 to 4500 (1.25 hours)? How would subreddit frontpages change? Would this favor breaking news or evergreen content?

2. What if we changed `log10` to `log2`? Now 2x more votes = +1 score instead of 10x. What posts would rise/fall?

3. Different subreddits have different tempos. r/news moves in minutes; r/history moves in days. Should the algorithm have subreddit-specific parameters?

4. The formula uses `upvotes - downvotes`. Controversial post (500 up, 500 down) = 0 score. Is this right? Should "engagement" matter?

**The insight:** Ranking algorithms encode values. Reddit's algorithm says "new content matters" and "vote magnitude matters logarithmically." Different constants = different community culture.

---

## Experiment 2: The Subreddit Discovery Problem

**Scenario:**
A user is interested in mechanical keyboards. How do they find r/MechanicalKeyboards?

**Questions to consider:**

1. Search by keyword finds subreddits with "keyboard" in the name. But what about r/MK (an abbreviation)? How do you handle community-specific jargon?

2. "Related subreddits" could use user overlap: "Users in r/MechanicalKeyboards also follow r/CustomKeyboards." How do you compute this at scale with 100,000 subreddits?

3. A subreddit should only appear in recommendations if it's "healthy" (not abandoned, not toxic). How do you measure community health automatically?

4. Private subreddits shouldn't be discoverable. But how do users find out about them? Word of mouth only? Is there a middle ground?

**The insight:** Discovery is a chicken-and-egg problem. Small subreddits need discovery to grow, but discovery algorithms favor large subreddits.

---

## Experiment 3: The Award Economy

**Scenario:**
Reddit has Gold, Platinum, and custom awards that users can give to posts/comments (costs real money).

**Questions to consider:**

1. Should awards affect ranking? "Gold = 100 extra upvotes" would let wealthy users influence frontpages. Is this okay?

2. Award fraud: User creates alt account, gives gold to their own posts. How do you detect this without surveilling users?

3. Subreddit-specific awards (custom emojis). How do you price these? Should popular subreddits get more revenue than small ones?

4. Awards as social proof: "This comment has 5 golds, must be good." Does this create a rich-get-richer effect? Should you hide awards until after a user votes?

**The insight:** Introducing money into a voting system changes incentives. Reddit's awards are a monetization experiment with community effects.

---

## Experiment 4: The Moderation Queue

**Scenario:**
r/AskScience has strict rules: Only peer-reviewed sources, no jokes, no anecdotes. They get 10,000 comments/day during popular threads.

**Questions to consider:**

1. AutoModerator catches 60% of rule violations. But 40% (4,000 comments/day) need human review. With 10 volunteer mods, that's 400 comments each. Is this sustainable?

2. A controversial post gets 1,000 comments in an hour. Should comments be visible immediately (risk of rule violations) or held until reviewed (kills discussion momentum)?

3. Moderators are volunteers with real jobs. They're most active 6-10 PM in their timezone. How do you ensure 24/7 coverage for a global subreddit?

4. A moderator goes rogue—approving spam, banning legitimate users. How do you detect moderator abuse without over-surveilling?

**The insight:** Human moderation doesn't scale linearly. Tools (AutoMod) help, but large communities face an inevitable bottleneck.

---

## Experiment 5: The r/all Algorithm

**Scenario:**
r/all is a cross-subreddit ranking. But a post that dominates r/funny might be invisible in r/all.

**Questions to consider:**

1. r/funny has 50M subscribers. r/obscurehobby has 5K. If you rank by raw votes, r/funny dominates r/all. How do you normalize?

2. Some subreddits are controversial (politics, NSFW). Should they appear on r/all? Who decides?

3. A post is #1 in r/funny and #1 on r/all. It gets different votes from r/funny users vs. r/all visitors. Should these count the same?

4. "Super Tuesday" — political subreddits flood r/all. Sports fans are annoyed. How do you balance timeliness (politics is happening now) vs. diversity (other topics matter too)?

**The insight:** r/all is a governance problem as much as a technical problem. It's a shared resource with competing communities.
