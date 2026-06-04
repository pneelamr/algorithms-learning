# 3-opt local search — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; better than 2-opt, slower
> **Used for:** Improving a tour via 3-edge reconnections for higher quality than 2-opt
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine your loop of string has a knot that uncrossing two strings can't fix. So instead you **cut the loop in three places**, ending up with three pieces of string. Now there are *several* ways to tie the three pieces back into one loop — including picking up a whole piece and **moving it somewhere else** in the loop, not just flipping it around.

3-opt tries all those ways of re-tying the three pieces and keeps whichever makes the loop shortest. Because you have more ways to re-tie than when you only cut in two places, you can fix tangles that the simpler method gets stuck on.

