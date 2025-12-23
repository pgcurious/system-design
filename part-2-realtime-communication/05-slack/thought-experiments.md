# Thought Experiments: Slack

These exercises test deep understanding of Slack's design principles.

---

## Experiment 1: The Enterprise Grid Problem

**Scenario:**
A 50,000-person corporation uses Slack Enterprise Grid. They have 500 workspaces (one per team/department), but executives need to see activity across all workspaces.

**Questions to consider:**

1. An executive searches for "Q4 budget" across all 500 workspaces. How does this query route? What's the latency if each workspace search takes 100ms?

2. The company wants a unified #company-announcements channel visible in all 500 workspaces. If the CEO posts, how many message writes occur? How many push notifications?

3. A user belongs to 15 workspaces. When they open Slack, what's the startup latency to fetch unread counts from all workspaces? How would you optimize this?

4. Compliance requires that no message can be deleted by users—only by admins after 7 years. How does this affect your storage and retention architecture?

**The insight:** Enterprise scale multiplies complexity. N workspaces means N× everything, unless you architect for cross-workspace operations.

---

## Experiment 2: The Thread vs. Channel Debate

**Scenario:**
Slack threads were added later. A thread is a conversation within a channel, but it creates split attention: You might miss important info in a thread if you're only watching the channel.

**Questions to consider:**

1. Design the data model for threads. Is a thread reply a separate message, or a child of the parent message? What are the tradeoffs?

2. When a thread gets a new reply, who should be notified? Everyone in the channel? Only thread participants? What's the UX tradeoff?

3. A thread has 500 replies. Loading the thread means fetching 500 messages. But the channel view should show just "N replies in thread." How do you efficiently display the channel view without loading all thread content?

4. Search should find messages in threads. But a thread reply says "I agree" (not searchable). Should search index thread context (the parent message)?

**The insight:** Threads seem simple but create complex UX and data model challenges. Slack's threaded messaging is a compromise with tradeoffs.

---

## Experiment 3: The Bot/Integration Explosion

**Scenario:**
A workspace has 100 active integrations (bots, apps, workflows). Each integration might post 100+ messages per day.

**Questions to consider:**

1. A buggy integration enters an infinite loop, posting 1,000 messages per second. How do you detect and stop this without breaking legitimate high-volume integrations?

2. Integrations have access tokens with permissions. An employee leaves and their token is in a shared script. How do you revoke access without breaking the integration for the team?

3. An integration reads all messages in #engineering (10,000 messages/day) to analyze sentiment. What's the API rate limit story? How do you prevent one integration from degrading service for everyone?

4. Slack Workflow Builder lets non-developers create automations. A user creates a workflow that triggers on every message in a channel. The channel gets busy. What happens?

**The insight:** Opening a platform to integrations means dealing with the pathological cases—infinite loops, leaked tokens, resource exhaustion.

---

## Experiment 4: The Real-Time Sync Problem

**Scenario:**
A user has Slack open on their laptop, phone, and in a browser tab. They edit a message on their laptop.

**Questions to consider:**

1. How does the edit propagate to the phone and browser? If the phone is briefly offline, what happens when it reconnects?

2. The user is typing a message on their laptop. The phone shows "User is typing..." but the user hasn't sent yet. How does the typing indicator work across devices?

3. The user reads a DM on their phone. The laptop should now show that DM as read. But the laptop is behind a VPN that delays traffic by 30 seconds. What happens in that 30 seconds?

4. The user has "Do Not Disturb" on until 9 AM. They open Slack on their laptop at 8:55 AM. Should Slack show notifications for messages that arrived during DND? How do you sync DND state across devices?

**The insight:** Multi-device sync is an eventual consistency problem. Users expect instant sync, but physics disagrees.

---

## Experiment 5: The Slack Connect Trust Problem

**Scenario:**
Acme Corp shares a channel with Vendor Inc. After a year, Acme discovers Vendor Inc was a front for a competitor. They want to leave the shared channel and delete all messages.

**Questions to consider:**

1. Acme leaves the shared channel. What happens to messages Acme employees sent? Should Vendor Inc still see them? Who owns those messages?

2. Acme wants to delete all messages its employees ever sent in the shared channel. But those messages are also in Vendor Inc's shard (mirrored). Can Acme force deletion in Vendor Inc's workspace?

3. Vendor Inc's admin exports the shared channel history before Acme leaves. Now they have a copy that Acme can't delete. What are the options?

4. Before sharing a channel, both parties must agree to terms. What data governance terms would you include? How do you enforce them technically (vs. just legally)?

**The insight:** Cross-organization data sharing creates ownership ambiguity. Technology can enforce some boundaries, but ultimately, trust is a legal and social problem.
