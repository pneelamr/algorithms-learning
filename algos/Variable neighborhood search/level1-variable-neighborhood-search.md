# Variable neighborhood search — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Clustering / p-median  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; systematic neighborhood changes
> **Used for:** Systematically switching neighborhood structures to balance diversify/intensify
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you've lost your keys. You search the spot you're standing in really carefully — nothing. Instead of giving up, you **widen your search**: check the whole room, then the whole floor, then the whole house. Each time you get stuck, you **look in a bigger, different area**. And the moment you find a clue, you go back to searching carefully right around it.

Variable neighborhood search does this for puzzles. When tidying up an answer gets stuck (no small change helps), it **switches to a different, usually bigger kind of change** to jump somewhere new, then goes back to careful tidying. By keeping different "sizes" of search and switching between them, it escapes spots where any single kind of search would get stuck.

