# Thought Experiments: Stripe Idempotency

These exercises test deep understanding of idempotency and payment systems.

---

## Experiment 1: The Key Generation Problem

**Scenario:**
You're implementing idempotency keys for a shopping cart checkout.

**Questions to consider:**

1. Key option: `user_id + cart_id`. User abandons cart, adds more items, checks out again. Same key, different amounts. What happens?

2. Key option: `user_id + cart_id + timestamp`. Every retry gets a new timestamp, so a new key. Defeats the purpose. How do you fix this?

3. Key option: Hash of the entire request body. If the server adds a field to the request (e.g., server timestamp), the hash changes. Now retries don't match.

4. What's the ideal key for: "This specific user attempting to pay this specific amount for this specific cart"?

**The insight:** Key generation must capture the semantic identity of the request, not its byte-level representation.

---

## Experiment 2: The Timeout Dilemma

**Scenario:**
Your payment API has a 30-second timeout. A charge to a foreign card takes 45 seconds. Client times out, retries.

**Questions to consider:**

1. The retry arrives while the original is still processing. The wait-for-completion logic waits. But the retry also has a 30-second timeout. Will it succeed?

2. You increase client timeout to 60 seconds. Users complain about slow checkout. What's the UX solution?

3. Some banks take 60+ seconds. Should you have different timeouts for different card types? How do you know which cards are slow before you try?

4. Alternative: Return immediately with "payment processing" status, and webhook when complete. How does idempotency work with async flows?

**The insight:** Synchronous APIs with idempotency are simpler but face timeout constraints. Async patterns are more complex but handle slow operations better.

---

## Experiment 3: The Webhook Idempotency Problem

**Scenario:**
Stripe sends a webhook for a successful payment. Your server receives it but crashes before saving. Stripe retries the webhook.

**Questions to consider:**

1. Webhooks have their own idempotency challenge. The same event might be delivered twice. How do you detect duplicate webhook deliveries?

2. You use the webhook's `event_id` as an idempotency key. You store processed event IDs in a database. How long do you keep them?

3. Webhook processing takes 5 seconds. Stripe's retry comes in 3 seconds (assuming failure). Now you're processing the same event twice concurrently.

4. Your webhook handler is not idempotent (it sends an email). You receive the same webhook twice. User gets two emails. How do you fix this?

**The insight:** Idempotency must be end-to-end. API calls are idempotent, but if your handlers aren't, you still have duplicates.

---

## Experiment 4: The Cross-System Transaction

**Scenario:**
Payment flow: (1) Charge card on Stripe, (2) Update inventory in your DB, (3) Send confirmation email.

**Questions to consider:**

1. Stripe charge succeeds, but inventory update fails (out of stock). What do you do? Refund the charge? Hold it?

2. You use the Saga pattern: Charge → Update inventory (if fail, refund) → Send email. The refund fails. Now what?

3. The email service is down. Payment and inventory are fine. Do you fail the whole transaction? Or proceed without email?

4. You want exactly-once: Charge once, update inventory once, send email once. Given network failures are always possible, is this achievable?

**The insight:** Distributed transactions across services are fundamentally hard. Idempotency helps within each service but doesn't solve cross-service coordination.

---

## Experiment 5: The Replay Attack

**Scenario:**
A malicious actor captures a legitimate payment request with idempotency key. They want to exploit it.

**Questions to consider:**

1. If they replay the same request, they get the cached response (already processed). No harm done? Or is there a problem?

2. What if the original request was declined? The cached response is "declined." Can the attacker retry after 24 hours with the same key and different card?

3. The request includes a card token. Card tokens expire. But the idempotency key doesn't include the token value. What's the interaction?

4. The attacker captures a request from User A and replays it for User B's session. If your system doesn't bind idempotency keys to users, what happens?

**The insight:** Idempotency is not a security mechanism. It must be combined with authentication, authorization, and request validation.
