# Ant colony optimization — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Network routing  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; often good on structured instances
> **Used for:** Pheromone-guided construction inspired by ant foraging
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Real ants find the shortest path to food without a map. Each ant wanders a bit randomly, and as it walks it leaves a **smell trail** (called pheromone). Ants that happen to take a **short** path get back sooner, so they lay down smell **more often** — making that trail stronger. Other ants smell the strong trail and follow it, making it stronger still. The smell on long, slow paths slowly **fades away**. Pretty soon the whole colony is marching along the shortest route.

Ant colony optimization copies this for puzzles like "what's the shortest tour through all these cities?" Lots of pretend ants each build a tour, the **good short tours get more smell**, the smell on bad routes fades, and round after round the ants' smell-map points more and more at a really good tour.

