# Christofides–Serdyukov — Level 1: ELI5

> **Example problems:** Metric Traveling Salesman Problem, Vehicle Routing Problem, Logistics / delivery route planning  ·  **Type:** Approximation  ·  **Guarantee:** At most 1.5× optimal (metric TSP)
> **Used for:** Balanced quality/speed approximation with a worst-case bound
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine the cheapest little web of roads that links all your friends' houses together. To turn it into a round trip you need every house to have an *even* number of road-ends — pairs that go in and out. A few houses are left with an odd, lonely road-end. Instead of copying *every* road (wasteful!), you just add a few smart extra roads that **pair up only the lonely houses** as cheaply as possible.

Now you can walk one big loop, skipping anyone you've already visited. Because you only patched the lonely spots instead of doubling everything, the trip you get is never worse than **one-and-a-half times** the very best trip.
