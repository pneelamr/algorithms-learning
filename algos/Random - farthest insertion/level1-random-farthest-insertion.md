# Random / farthest insertion — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Multi-start metaheuristic seeding  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound
> **Used for:** Generating diverse starting tours by inserting far/random nodes early
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

You have a loop of string with some friends on it and a pile still waiting. The other tie-in games asked for the *closest* waiting friend. These two play the opposite (or a coin-flip):

- **Farthest:** add the friend who is **farthest away** first — the ones at the far corners. That quickly stretches the loop out to its full *outline*, and after that everyone left is easy to tuck in.
- **Random:** just **pick a waiting friend at random** and tie them in cheaply.

Both still tie each new friend in at the spot that grows the loop the least. Doing "farthest first" tends to give the **neatest** loop, and "random" lets you make **lots of different** loops to try.

