# Thought Experiments: Stock Exchange

These exercises test deep understanding of exchange systems and financial infrastructure.

---

## Experiment 1: The Fairness Debate

**Scenario:**
A trader argues: "High-frequency traders with faster connections get to see orders first and trade against them. This is unfair to regular investors."

**Questions to consider:**

1. Is "first come, first served" (price-time priority) actually fair? What if "first" is determined by millions of dollars in infrastructure?

2. IEX exchange introduced a "speed bump" (350 microsecond delay). How would you implement this? Where in the system does the delay happen?

3. Batch auctions (all orders in a window matched at once) eliminate speed advantages. What's the tradeoff? Why don't all exchanges use this?

4. If you remove speed advantages, HFT firms provide less liquidity. Less liquidity means wider spreads and worse prices for everyone. How do you balance?

**The insight:** "Fairness" in markets is a policy choice with tradeoffs. Different exchange designs embody different fairness philosophies.

---

## Experiment 2: The Dark Pool Problem

**Scenario:**
Large institutional investors use "dark pools" (private exchanges that don't show order books) to avoid revealing their intentions.

**Questions to consider:**

1. On a public exchange, if you show a buy order for 1 million shares, everyone knows. Price moves against you before you can fill. How does a dark pool solve this?

2. Dark pools match orders without revealing prices. How do you determine a fair price? (Hint: They often use the "mid-point" of the public exchange.)

3. If too much trading moves to dark pools, the public exchange has less liquidity, and the mid-point becomes less reliable. Is there a tipping point?

4. Regulators require dark pools to report trades after they happen. How does this "post-trade transparency" differ from "pre-trade transparency"?

**The insight:** Transparency in markets is a spectrum. Dark pools trade transparency for execution quality.

---

## Experiment 3: The Cross-Border Settlement

**Scenario:**
A US investor buys stock on the Tokyo Stock Exchange. The trade involves USD, JPY, US broker, Japanese broker, US settlement, and Japanese settlement.

**Questions to consider:**

1. Settlement takes T+2 (2 days after trade). During those 2 days, currency rates change. Who bears the FX risk? How do you hedge?

2. Time zones: Tokyo market closes before US market opens. If there's a price discrepancy, who can arbitrage? How fast?

3. Each country has its own settlement system (DTC in US, JASDEC in Japan). How do they reconcile cross-border trades?

4. A failure in one settlement system cascades internationally. What happens if JASDEC is down when a US investor's Tokyo trade needs to settle?

**The insight:** Global markets are interconnected but settlements are local. This creates complexity and systemic risk.

---

## Experiment 4: The Market Maker's Dilemma

**Scenario:**
You're a market maker. You must continuously quote buy and sell prices for a stock. You make money on the spread.

**Questions to consider:**

1. News breaks: A company announces bankruptcy. Your sell quote is now stale (too high). Fast traders buy from you before you can update. How do you protect yourself?

2. If you quote narrow spreads, you get more trades but more risk. If you quote wide spreads, you get fewer trades but safer. How do you optimize?

3. Your algorithm has a bug: It quotes $100 when it meant $10. Millions of shares trade at the wrong price. Can you cancel these trades? Should you be able to?

4. Regulators require market makers to provide liquidity even during market stress. But market stress is exactly when you most want to pull quotes. How do you comply?

**The insight:** Market makers provide a public good (liquidity) but are private, profit-seeking firms. This creates inherent tension.

---

## Experiment 5: The Blockchain Exchange

**Scenario:**
A new exchange proposes: "All orders on a public blockchain. Matching is decentralized. No central exchange."

**Questions to consider:**

1. Blockchain confirmation takes seconds to minutes. Stock trades need microseconds. How do you reconcile?

2. If orders are public (on the blockchain), everyone sees them. Frontrunning becomes trivial. How do you prevent this?

3. Who runs the matching? If it's decentralized (like Ethereum validators), different validators might produce different results. How do you ensure determinism?

4. A smart contract bug allows someone to drain all funds. Traditional exchanges have legal recourse. Decentralized exchanges might not. What's the governance model?

**The insight:** Decentralization solves some problems (single point of failure) but creates others (latency, determinism, governance). It's not universally better.
