# Greedy edge-selection (cheapest link) — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Circuit / cable layout  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; depends on instance
> **Used for:** Constructing a tour by adding cheapest valid edges first
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you have a big pile of roads between friends' houses, each with a price tag. You want to build one big loop that visits everyone. So you keep grabbing the **cheapest road left** and adding it — but only if it doesn't break two rules: no house can have three roads, and you can't make a small loop that closes too early before everyone's in.

Keep grabbing the cheapest allowed road until all the houses are strung together into one single loop. That's cheapest-link: always take the best bargain road that still lets the loop finish.
