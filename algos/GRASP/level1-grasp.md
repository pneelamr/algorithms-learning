# GRASP — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Set covering, Scheduling  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; multi-start greedy + local search
> **Used for:** Multi-start randomized-greedy construction followed by local search
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine building a tower out of blocks, and you want the best tower. You build one tower by always grabbing a **pretty good** block each step — but not always *the* best one; sometimes you pick from the **top few**, just to mix it up. Then you nudge the finished tower to make it a little sturdier. Then you **knock it down and build a whole new one** the same way, with different lucky picks. Do this many times and **keep the best tower** you ever built.

GRASP does this for puzzles. Each round it **builds a fresh answer with a mix of smart-and-random choices**, then **tidies it up**, then starts over. Lots of fresh starts means lots of different answers — and you just keep the best one.

