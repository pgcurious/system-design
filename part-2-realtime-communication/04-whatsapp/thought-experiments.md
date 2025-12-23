# Thought Experiments: WhatsApp

These exercises test deep understanding of WhatsApp's design principles.

---

## Experiment 1: The Billion-Connection Problem

**Scenario:**
WhatsApp has 1 billion concurrent connections. Each connection needs 15 KB of memory. You have servers with 256 GB RAM.

**Questions to consider:**

1. Calculate the minimum number of servers needed just for connection handling. What's the utilization target you'd aim for, and why not 100%?

2. If a server holds 2 million connections and crashes, 2 million users are disconnected. What's your failover strategy? How long until those users are reconnected?

3. Users are distributed globally. Do you route everyone to the nearest data center, or assign users to specific regions? What are the tradeoffs?

4. A message from User A (connected to Tokyo server) to User B (connected to Frankfurt server) must cross the Pacific. Estimate the latency and compare to two users in the same data center.

**The insight:** Global scale means every design decision has a geographic component. Latency is physics.

---

## Experiment 2: The Delivery Guarantee

**Scenario:**
A user sends a critical message: "Emergency, call me now." The recipient's phone is in airplane mode. Design the delivery system.

**Questions to consider:**

1. How long do you keep the message in the offline queue? Forever? 30 days? What's the storage cost implication?

2. The recipient was offline for 2 weeks, then connects. They have 5,000 messages queued. Do you push all at once, or batch? What's the UX impact of each approach?

3. The sender's message says "Emergency" but your servers can't read it (E2EE). How do you prioritize delivery when you don't know content priority?

4. The recipient's device stores messages locally. If their phone is lost/stolen, those messages are exposed. How does WhatsApp handle this? (Hint: Look into backup encryption.)

**The insight:** "Never lose a message" sounds simple, but edge cases multiply when you consider offline scenarios, encryption, and multi-device.

---

## Experiment 3: The Spam/Safety Problem

**Scenario:**
You can't read messages (E2EE), but spam and abuse are rampant. Design a system to detect and limit abuse.

**Questions to consider:**

1. You can't analyze message content. What signals *can* you observe? (Hint: Think about metadata—who messages whom, how often, from where.)

2. A new user joins and immediately sends the same message to 1,000 different numbers. Without reading the message, what can you infer?

3. You implement rate limiting: Max 100 messages/day to non-contacts. A teacher legitimately needs to message 200 new parents. How do you handle this?

4. WhatsApp allows forwarded message labels and limits on highly-forwarded messages. How do you implement forward tracking without breaking E2EE?

**The insight:** Privacy and safety are in tension. WhatsApp's compromise: Analyze patterns and metadata, never content.

---

## Experiment 4: The Multi-Device Challenge

**Scenario:**
WhatsApp now supports 4 linked devices (phone + 3 companions). A message sent to you must appear on all 4 devices.

**Questions to consider:**

1. With E2EE, sender must encrypt the message 4 times (once per device's key). Sending to a group of 10 people, each with 4 devices = 40 encrypted copies. What's the bandwidth cost?

2. You send a message from your phone, then immediately check your laptop. But the laptop doesn't show it yet. Why? How do you sync state across devices?

3. You delete a message on your phone. Should it be deleted on all your devices? What's the protocol for this? (Remember: E2EE means the server can't interpret "delete message X".)

4. You lose your phone but keep using WhatsApp on laptop. A week later, you get a new phone. How do you restore your message history? Where was it stored?

**The insight:** Multi-device E2EE is genuinely hard. The Signal Protocol needed a major extension (Sesame) to handle this.

---

## Experiment 5: The Voice/Video Call Problem

**Scenario:**
Voice calls require 32 Kbps audio stream. Video calls require 1-3 Mbps. WhatsApp handles millions of concurrent calls.

**Questions to consider:**

1. Should call audio/video flow through WhatsApp servers, or peer-to-peer directly between devices? What are the privacy, quality, and infrastructure tradeoffs?

2. If peer-to-peer, how do two devices behind NAT (home routers) discover each other's addresses? (This is the STUN/TURN problem.)

3. Group video call with 8 participants. Each person's video must reach 7 others. Peer-to-peer = 56 streams per call. Is that feasible? What's the alternative?

4. Call quality adapts to network conditions. You're on a train, bandwidth drops. How does the codec adapt? Who decides—sender, receiver, or server?

**The insight:** Real-time media is a different beast from messaging. Latency tolerance is measured in tens of milliseconds, not seconds.
