# Branch-and-bound — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, 0/1 Knapsack, Integer programming, Job scheduling  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour with pruning based on bounds
> **Used for:** General exact search that prunes provably bad subtrees
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine looking for the shortest way to visit all your friends' houses. Instead of trying every route, you start building routes step by step. Whenever a half-finished route is **already longer** than a full route you've already found, you stop right there — there's no point finishing it, it can only get worse.

That's branch-and-bound: it splits the search into choices (**branch**), and the moment a choice can't possibly beat your best answer so far (**bound**), it throws that whole pile of routes away without checking them. It still finds the perfect answer — just by cleverly skipping the hopeless ones.
