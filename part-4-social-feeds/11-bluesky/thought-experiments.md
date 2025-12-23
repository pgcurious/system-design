# Thought Experiments: Bluesky

These exercises test deep understanding of decentralized social network design.

---

## Experiment 1: The Portability Promise

**Scenario:**
Alice has 100,000 followers on bsky.social. She wants to move to self-hosted PDS.

**Questions to consider:**

1. Alice updates her DID document to point to her new PDS. But her followers' App Views are caching the old endpoint. How long until everyone sees her posts from the new server?

2. Alice's post history is 5 GB. She needs to export from bsky.social and import to her PDS. What if bsky.social rate-limits exports? What if they charge for it?

3. Alice's followers follow her DID. But what if some followers' clients don't support DID resolution correctly and still point to bsky.social?

4. The "bsky.social" branding is in Alice's handle. After moving, she wants to be "alice.com." Is this a handle change or just a PDS change? How do followers' UIs update?

**The insight:** Portability is technically possible but has UX friction. The gap between "possible" and "easy" determines whether users actually exercise their rights.

---

## Experiment 2: The Feed Generator Trust Problem

**Scenario:**
You subscribe to "BestAINews" feed generator. It starts inserting sponsored posts without disclosure.

**Questions to consider:**

1. How do you detect that a feed generator is inserting non-organic content? The feed is just a ranked list of posts—how do you know if ranking is honest?

2. Could feed generators be required to be transparent about their algorithms? How would you enforce this in a decentralized system?

3. A feed generator with 1 million subscribers has enormous influence. It could manipulate elections by suppressing/promoting content. Who holds feed generators accountable?

4. The App View could refuse to serve certain feed generators. But this introduces centralization at the App View layer. Is this acceptable?

**The insight:** Decentralizing the protocol doesn't decentralize power. Feed generators and App Views become new power centers.

---

## Experiment 3: The Moderation Coordination Problem

**Scenario:**
A harassment campaign is launched against a user. Hundreds of accounts are involved, spread across 50 different PDSs.

**Questions to consider:**

1. The victim blocks all harassers. But new accounts are created on permissive PDSs. How do you stop whack-a-mole harassment?

2. A labeler identifies the harassers and labels them "harassment." But some PDSs refuse to share data with labelers. What now?

3. Should App Views refuse to serve content from PDSs that don't cooperate with moderation? This starts to look like centralized moderation again.

4. The victim wants the harassment posts deleted, not just hidden. But the posts live on hostile PDSs. Is deletion possible in a decentralized system?

**The insight:** Decentralization makes coordinated moderation harder. The tradeoff is real: more user freedom vs. more potential for abuse.

---

## Experiment 4: The Economic Sustainability Problem

**Scenario:**
Running infrastructure costs money. Bluesky the company currently subsidizes everything. What happens when VC money runs out?

**Questions to consider:**

1. bsky.social announces $5/month for accounts. But anyone can run a free PDS. Does bsky.social become a ghost town?

2. Alternative: bsky.social remains free but shows ads. But other PDSs don't show ads. Do advertisers pay less because users can escape ads?

3. Feed generators are compute-intensive. Who pays? The user? The feed generator operator? What's the sustainable model?

4. The relay processes every event on the network. At scale, this costs millions/year. Who funds public infrastructure in a decentralized network?

**The insight:** Open protocols can be "tragedy of the commons." Someone has to pay for infrastructure, but everyone has incentive to free-ride.

---

## Experiment 5: The Protocol Evolution Problem

**Scenario:**
AT Protocol v2 is proposed with breaking changes. Some PDSs upgrade, some don't.

**Questions to consider:**

1. A v2 PDS publishes a post using a new record type. A v1 App View doesn't understand it. What does the user see?

2. The DID document format changes in v2. Old clients can't resolve new DIDs. How do you migrate without breaking old clients?

3. Governance: Who decides what goes into AT Protocol v2? Bluesky the company? A foundation? A rough consensus process like IETF?

4. If a protocol change is controversial (e.g., adding DRM), can a fork of the protocol survive? What happens to the network effect?

**The insight:** Protocol governance is political. "Decentralized" doesn't mean "no one is in charge"—it means the power dynamics are different.
