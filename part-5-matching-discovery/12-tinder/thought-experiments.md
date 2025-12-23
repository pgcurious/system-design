# Thought Experiments: Tinder

These exercises test deep understanding of geospatial matching systems.

---

## Experiment 1: The Cold Start Problem

**Scenario:**
A new user signs up. They have no swipe history, no ELO score, and unknown preferences (beyond stated age/gender).

**Questions to consider:**

1. What recommendations do you show a brand new user? Random selection? Local popular users? How do you avoid overwhelming popular users with all the new user traffic?

2. The new user swipes right on everyone (hoping for any match). How do you interpret this? High desirability? Desperation? How does this affect their ELO?

3. The new user swipes left on everyone (very picky). Do you run out of recommendations? Do you lower your quality bar?

4. After 50 swipes, you have some signal. How do you recalibrate recommendations? How aggressively do you update ELO?

**The insight:** Cold start is a chicken-and-egg problem. You need swipe data to calibrate, but you need good recommendations to get swipe data.

---

## Experiment 2: The Asymmetric Market Problem

**Scenario:**
In many markets, one gender swipes right far more liberally than the other. This creates massive asymmetry.

**Questions to consider:**

1. If men swipe right on 60% of profiles and women swipe right on 6%, what's the match rate? What's the experience like for each side?

2. Should the algorithm compensate? E.g., show women to fewer men so their inboxes aren't overwhelmed? Is this fair?

3. ELO-style scoring will give high scores to women (who are selective) and low scores to men (who are not). Does this match reality, or is it a measurement artifact?

4. A paid feature: "See who liked you." For the selective side, this is very valuable. For the liberal side, it's not. How do you price this?

**The insight:** Two-sided markets have inherent asymmetries. The algorithm and monetization must account for different experiences on each side.

---

## Experiment 3: The Dense Urban Problem

**Scenario:**
Manhattan: 1 million potential matches within 5 miles. Rural Montana: 100 potential matches within 50 miles.

**Questions to consider:**

1. In Manhattan, you can't show all 1 million people. What's the selection strategy? Top N by ELO? Random sample? Rotate over time?

2. In rural Montana, you might exhaust all options in a week. What then? Expand radius? Show people further away? Tell the user "no more nearby"?

3. A Manhattan user swipes 500 times/day. A Montana user swipes 50 times/day. How does this affect your engagement metrics? Your recommendations?

4. A user travels from Manhattan to Montana. Their expectations are calibrated for dense markets. How do you manage the UX of suddenly having few options?

**The insight:** One algorithm doesn't fit all markets. Dense and sparse markets have fundamentally different dynamics.

---

## Experiment 4: The Bot/Catfish Problem

**Scenario:**
Fake profiles (stolen photos, automated behavior) pollute the system.

**Questions to consider:**

1. A profile has a suspiciously high right-swipe rate from viewers. Is this a very attractive person or a catfish using stolen photos? How do you distinguish?

2. Bots swipe right on everyone to harvest match data (for spam). How do you detect automated swiping patterns vs. humans who are just very active?

3. Photo verification (selfie matching face) helps but isn't perfect. How do you handle users who refuse verification? Reduce their visibility?

4. A sophisticated catfish uses real-time video chat but a face filter. They pass photo verification. What's next?

**The insight:** Trust and safety is an arms race. Every detection mechanism can be evaded with enough effort.

---

## Experiment 5: The Algorithmic Fairness Problem

**Scenario:**
Your ranking algorithm is trained on swipe data. But historical swipe data may reflect societal biases.

**Questions to consider:**

1. If users of certain races receive fewer right-swipes historically, the algorithm will learn to show them less. Is this the algorithm being "accurate" or perpetuating bias?

2. You could add a fairness constraint: "Show all races equally." But if this leads to worse match rates for users, is it actually helpful?

3. Some users have explicit racial preferences. Do you enforce them (respecting user choice) or override them (combating bias)?

4. An audit reveals certain demographics are systematically shown lower-quality recommendations. How do you investigate? How do you fix without introducing new biases?

**The insight:** Matching algorithms optimize for engagement, which reflects existing preferences—including biases. Deciding what to optimize for is a values question.
