# Nearest neighbor — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Online / greedy route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; often decent but can be poor
> **Used for:** Building a quick initial tour by always going to the closest unvisited stop
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you're visiting friends' houses. From wherever you're standing, you just walk to the **closest friend you haven't seen yet**. Then from there, the closest one you still haven't seen. And again, and again — always the nearest new house — until you've visited everyone and head home.

That's nearest neighbor: keep hopping to whoever's closest right now. It's super quick and easy, but because it never looks ahead, it can leave one faraway friend for the very end and force a long trip home.
