# Simulated annealing — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, VLSI circuit placement, Job-shop scheduling, Continuous function optimization  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Escaping local minima by accepting worse moves with cooling probability
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you're trying to settle a bumpy blanket into the lowest, flattest spot on a lumpy bed. If you only ever let the blanket slide *downhill*, it gets stuck in the first little dip — not the deepest one.

So instead you **shake the bed**. At first you shake it **hard**, so the blanket can bounce *out* of small dips and even go uphill for a moment to find a better valley. Then you shake **softer and softer**, until at the end you barely shake at all and the blanket settles into a really deep spot.

Simulated annealing is that trick for puzzles: take a guess, make little changes, **sometimes accept a worse change** (especially early, when you're "shaking hard"), and slowly stop accepting worse ones — so you wiggle out of bad spots and settle into a great answer.

