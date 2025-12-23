# Thought Experiments: Uber Nearby Drivers

These exercises test deep understanding of real-time geospatial systems.

---

## Experiment 1: The Airport Queue Problem

**Scenario:**
100 drivers are waiting at the airport parking lot. A rider requests a ride from the terminal. All 100 drivers are "nearby" (within 1 mile).

**Questions to consider:**

1. If you match the closest driver, the other 99 might have been waiting longer. Is distance the right metric, or should waiting time matter?

2. Airports have designated pickup queues. How do you integrate physical queue rules with digital matching? What if a driver leaves the queue to get coffee?

3. Airport rides are often long and lucrative. Drivers "camp" at airports. How do you prevent gaming where drivers fake nearby locations to cut the queue?

4. The rider wants a specific car type (SUV). Only 10 of the 100 drivers have SUVs. How do you balance queue fairness with rider preferences?

**The insight:** "Nearest driver" is simplistic. Fair matching considers wait time, driver expectations, and physical-world constraints.

---

## Experiment 2: The Rush Hour Cascade

**Scenario:**
5 PM in Manhattan. Everyone leaves work simultaneously. 100,000 ride requests in 15 minutes. Only 10,000 available drivers.

**Questions to consider:**

1. 90% of requests can't be matched immediately. Do you queue them? Show "no cars available"? Start surge pricing instantly?

2. Surge pricing attracts more drivers, but prices might 5x before supply responds. What's the maximum acceptable surge? Is there a cap?

3. Some riders are going 2 miles; some are going 20 miles. If you're short on drivers, should you prioritize short trips (more riders served) or long trips (more revenue)?

4. Drivers finishing trips are about to become available. Should riders wait 2 minutes for a nearby driver finishing a trip, or immediately get a far-away driver?

**The insight:** Scarcity creates hard choices. Dispatch optimization must consider not just current state but predicted near-future state.

---

## Experiment 3: The Network Partition

**Scenario:**
Due to a cell tower outage, drivers in downtown area lose network connectivity for 10 minutes. The location service has stale data.

**Questions to consider:**

1. Riders request rides downtown, but driver locations are 10 minutes stale. Matched drivers are no longer where they appeared. What's the rider experience?

2. Drivers reconnect after 10 minutes and flood the system with location updates. How do you handle the spike?

3. During the outage, you might have matched drivers to rides they couldn't accept (no connectivity). How do you handle unacknowledged trip offers?

4. Post-outage, some drivers are in the middle of trips they accepted before the outage. How do you distinguish "driver is offline" from "driver is on a trip but lost connectivity"?

**The insight:** Connectivity is not binary. Partial, degraded, and delayed connectivity create complex failure modes.

---

## Experiment 4: The Multi-Modal Future

**Scenario:**
Uber now includes scooters, bikes, public transit, and cars. A rider asks for "fastest" route from A to B.

**Questions to consider:**

1. The fastest might be: Scooter 5 minutes to subway, subway 10 minutes, walk 2 minutes. How do you model and compare multi-modal ETAs?

2. You dispatch a scooter, but someone else takes it before the rider arrives. Unlike cars, scooters don't come to you. How do you handle reservation vs. first-come-first-served?

3. Different modalities have different location update frequencies. Cars: every 4 seconds. Scooters: every 30 seconds. Bikes: when unlocked. How does this affect freshness?

4. Pricing: Cars are dynamic (surge). Scooters are fixed per minute. Public transit is separate. How do you show a unified price for a multi-modal trip?

**The insight:** Multi-modal transportation multiplies complexity. Each modality has different matching semantics, pricing, and availability models.

---

## Experiment 5: The Autonomous Vehicle Transition

**Scenario:**
Uber deploys 1,000 autonomous vehicles alongside 100,000 human drivers in a single city.

**Questions to consider:**

1. AVs can be pre-positioned (no driver cost to move). Human drivers go where they want. How do you balance AV positioning with human driver freedom?

2. AV pickup is slower (precise positioning required). Humans might grab a request before the AV arrives. How do you prevent wasted AV trips?

3. Riders might prefer human drivers (conversation, flexibility) or AVs (consistency, no tipping). Should you let riders choose? Does this create a two-tier system?

4. An AV breaks down mid-trip. Unlike a human driver who can call for help, the AV just stops. What's the fallback? How do you get the rider to their destination?

**The insight:** Mixing autonomous and human fleets creates heterogeneous supply. Dispatch must account for different capabilities and failure modes.
