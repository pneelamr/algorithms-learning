# Tabu search — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Job-shop scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; avoids cycling
> **Used for:** Local search with memory of recent moves to avoid revisiting solutions
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you're wandering a maze trying to find the exit, and you keep accidentally walking in circles back to the same spots. So you start **dropping a breadcrumb** on each path you just took and make a rule: **"don't step back onto a breadcrumb I dropped in the last few steps."** Now you can't immediately retrace your steps, so you're forced to try *new* paths — and you stop going in circles.

Tabu search does this for puzzles. It improves an answer step by step, but it keeps a little **memory of its recent moves and refuses to undo them for a while** (those moves are "tabu" — forbidden). That memory pushes it to keep exploring new answers instead of getting stuck bouncing between the same few.

