# Thought Experiments: URL Shortener

These exercises test deep understanding of URL shortener design principles.

---

## Experiment 1: The Custom Domain Challenge

**Scenario:**
Enterprise customers want to use their own domains: `go.acme.com/xyz` instead of `short.ly/xyz`. You now have 1,000 enterprise customers, each with their own domain.

**Questions to consider:**

1. How do you route requests? DNS points `go.acme.com` to your servers, but how do you know which customer database to query?

2. Customer A and Customer B both create short links. Can they have the same short code (`xyz`)? How does this affect your database schema?

3. An enterprise customer churns and stops paying. What happens to their existing short links? Do you keep them working? For how long?

4. A customer's domain gets flagged as malicious by Google Safe Browsing (because someone created bad links on it). How do you isolate the blast radius?

**The insight:** Multi-tenancy at the URL level requires careful namespace management. Short codes aren't globally unique—they're unique per domain.

---

## Experiment 2: The Analytics Pipeline

**Scenario:**
Your marketing team wants real-time analytics: clicks per minute, geographic breakdown, referrer tracking. Currently you have 10,000 clicks/second.

**Questions to consider:**

1. You can't write to the database for every click (10,000 writes/second would melt it). What's the architecture?

2. A dashboard shows "Clicks in last 5 minutes." You're processing events with Kafka. Consumer lag is 30 seconds. What does the user see? How do you communicate this?

3. A link gets 1 million clicks in an hour. Storing every click is expensive. Do you:
   - Store every click (accurate, expensive)
   - Sample 1% and multiply by 100 (cheap, approximate)
   - Pre-aggregate per minute (middle ground)
   What's the business tradeoff?

4. GDPR requires you to delete a user's data on request. Their clicks are scattered across your analytics warehouse. How expensive is this deletion?

**The insight:** Real-time analytics at scale is about choosing the right tradeoffs between accuracy, latency, and cost.

---

## Experiment 3: The Spam Problem

**Scenario:**
Your URL shortener is being abused. Spammers create millions of links to phishing sites. Your domain is now flagged by Gmail as potentially dangerous.

**Questions to consider:**

1. You integrate with Google Safe Browsing to check URLs before creating short links. What's the latency impact? What's your fallback if Safe Browsing is down?

2. A sophisticated spammer creates a link to a legitimate site, gets it approved, then changes the legitimate site to redirect to phishing. How do you catch this?

3. Rate limiting by IP address is easy to bypass with rotating IPs. What other signals can you use to detect spam accounts?

4. You decide to require CAPTCHA for link creation. What's the impact on legitimate API users? How do you balance security with usability?

**The insight:** Security and usability are always in tension. Every protection has a bypass; the goal is raising the cost of abuse.

---

## Experiment 4: The Private Link Feature

**Scenario:**
Users want "private" short links that require a password to access. `short.ly/xyz` prompts for a password before redirecting.

**Questions to consider:**

1. Where do you store the password? In the same database as the URL mapping? Hashed with what algorithm?

2. The redirect flow now has an interstitial page for password entry. What happens to your cache? Can you still use CDN edge caching?

3. A user forgets their password. Can you have "password reset for a short link"? How do you authenticate the owner?

4. Someone brute-forces passwords on private links (try common passwords). How do you rate limit without knowing which link is being targeted? (The attacker can target different short codes to avoid per-link rate limits.)

**The insight:** Adding features to a simple system often has non-obvious infrastructure implications. Security features especially complicate caching.

---

## Experiment 5: The Zero-Click Attribution Problem

**Scenario:**
Mobile apps "deep link" into other apps. User clicks `short.ly/app-promo`, which should open the retailer's app directly (not the website). But how do you know if the app opened successfully?

**Questions to consider:**

1. User clicks link on Android. You redirect to `intent://` scheme to open the app. If the app isn't installed, nothing happens—user sees blank page. How do you detect this and fallback to Play Store?

2. You decide to use JavaScript to detect if app opened (wait 2 seconds, if still on page, redirect to fallback). What's the UX impact? What about users with JavaScript disabled?

3. Attribution: Marketing wants to know "this click led to an app install and $50 purchase." But once the user is in the app, you're out of the picture. How does attribution work in this model?

4. Apple's App Tracking Transparency (ATT) now requires user consent for cross-app tracking. How does this affect your attribution system?

**The insight:** URL shorteners that support deep linking become critical infrastructure for mobile attribution, which is a complex and privacy-sensitive domain.
