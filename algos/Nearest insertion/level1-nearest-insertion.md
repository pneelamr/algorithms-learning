# Nearest insertion — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Drilling / pick-path planning  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by inserting the nearest outside node at lowest cost
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you already have a little loop of string connecting a few friends' houses. Now you want to add a new friend. You find the friend who lives **closest to your loop**, then gently **snip open the nearest piece of string and tie them in** — right where they fit best, so the loop barely grows.

That's nearest insertion: keep finding the closest outside house and slotting it into the loop at the cheapest spot, growing one neat loop until everyone's on it.
