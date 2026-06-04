# MST doubling (tree-doubling) — Level 1: ELI5

> **Example problems:** Metric Traveling Salesman Problem, Vehicle Routing Problem, Network / route design  ·  **Type:** Approximation  ·  **Guarantee:** At most 2× optimal (metric TSP)
> **Used for:** Fast approximate routing with a worst-case guarantee
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine all your friends' houses connected by the **cheapest possible web of roads** that still links everyone together — no wasted roads, just enough to reach every house. Now walk along that web, tracing every road, to visit everyone and come back. If you ever reach a house you've already seen, you just **skip straight ahead** to the next new one.

That's MST doubling: build the cheapest connecting web, walk around it, and take shortcuts past places you've already visited. The trip you get is never worse than **twice** the best possible trip — and it's quick to figure out.
