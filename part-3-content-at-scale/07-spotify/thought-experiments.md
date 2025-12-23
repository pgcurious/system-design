# Thought Experiments: Spotify

These exercises test deep understanding of audio streaming and recommendation systems.

---

## Experiment 1: The Cold Start Problem

**Scenario:**
New user signs up. They have no listening history. You need to recommend songs immediately.

**Questions to consider:**

1. What signals can you use before the user plays anything? (Hint: Demographics, device type, signup time, country)

2. The user selects "I like rock and pop" during onboarding. How much better does this make recommendations vs. a totally blank slate?

3. After 1 song play, 10 song plays, 100 song plays—how does recommendation quality improve? When do you switch from onboarding mode to personalized mode?

4. A user lies about preferences (selects classical but actually likes hip-hop). How quickly can you detect this, and how aggressively should you override their stated preferences?

**The insight:** Cold start is a UX problem as much as an ML problem. Getting something decent fast matters more than waiting for perfect recommendations.

---

## Experiment 2: The Skip Signal

**Scenario:**
User plays song, skips at 15 seconds. What does this mean?

**Questions to consider:**

1. Is a skip always negative? What if the user has already heard the song 50 times?

2. Spotify tracks where in the song the skip happens. Skip at 3 seconds vs. skip at 55 seconds of a 60-second song—what's the difference in signal?

3. You want to avoid recommending songs users will skip. But if you never recommend new songs, users never discover music. How do you balance exploitation (play safe choices) vs. exploration (try new things)?

4. A user skips 10 songs in a row. Should you:
   - Keep trying different songs?
   - Ask "What would you like to hear?"
   - Play their most-listened song?
   How does this affect session length and satisfaction?

**The insight:** Implicit signals (skips, replays, time of day) often matter more than explicit signals (likes, saves). But implicit signals are noisy.

---

## Experiment 3: The Royalty Calculation

**Scenario:**
Spotify pays artists per stream. A user listens to a song for 30 seconds—the minimum to count as a "stream."

**Questions to consider:**

1. How do you accurately count streams at 1 billion streams per day? What's the difference between "approximately 1 billion" and "exactly 1,000,000,012"?

2. Fraud: Someone creates fake accounts and loops their own song 24/7. How do you detect this? What patterns distinguish fake streams from viral hits?

3. The stream count must be auditable—artists and labels verify payments. What's the data pipeline from play event to royalty report? How do you ensure no streams are double-counted or lost?

4. A legal requirement: Retain stream data for 7 years for audit. At 1 billion events/day, how much storage is that? How do you query this efficiently for an artist's annual royalty statement?

**The insight:** Payments turn technical accuracy into legal requirements. "Close enough" isn't good enough when money is involved.

---

## Experiment 4: The Playlist Takeover

**Scenario:**
Spotify-curated playlists (Today's Top Hits, RapCaviar) have millions of followers. Placement on these playlists makes or breaks careers.

**Questions to consider:**

1. A playlist has 5 million followers. You add a new song to position 1. Assuming 1% daily active rate, that's 50,000 immediate plays. How do you ensure CDN can handle the spike?

2. Playlist order affects stream counts. Position 1 gets 10x the plays of position 20. How does the playlist team decide position? Should it be transparent?

3. Gaming: A label tries to manipulate playlist inclusion by buying fake streams for a song. Can you detect pre-release manipulation?

4. A/B testing playlists: You want to test whether Song A or Song B in position 1 performs better. How do you split the audience? What do you measure?

**The insight:** Editorial curation (playlists) and algorithmic curation (recommendations) interact in complex ways. Human editors have enormous power over what becomes popular.

---

## Experiment 5: The Podcast Problem

**Scenario:**
Podcasts are fundamentally different from music: Long-form (1+ hour), sequential (episodes in order), less replay value.

**Questions to consider:**

1. Music caching: Cache the most-replayed songs. Podcast caching: Cache... what? Users rarely re-listen. How does this change your caching strategy?

2. Podcast files are 50-200 MB (vs. 5 MB songs). If a user starts a podcast, they might not finish for days. How does streaming vs. progressive download change?

3. Podcast discovery: Music has "songs like this." Podcasts have... what? The content is voice, hard to analyze. How do you recommend podcasts?

4. Ads in podcasts are embedded in the audio (host-read) vs. dynamically inserted. For dynamic ads, you need to splice audio on-the-fly. What's the architecture?

**The insight:** Extending a platform to new content types reveals hidden assumptions. What works for music (short, replayed, sound-based) fails for podcasts.
