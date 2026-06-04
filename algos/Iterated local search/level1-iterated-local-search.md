# Iterated local search — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; layered on local search
> **Used for:** Repeatedly perturbing then re-optimizing a local optimum
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you slide a marble down a bumpy hill and it settles in a little dip. To find a *deeper* dip, you don't pick the marble up and throw it somewhere totally random — you just give the hill a **small nudge**, so the marble hops over a bump into a nearby spot, and then let it **roll downhill again** to settle. If the new dip is deeper, you keep it; if not, you go back. Nudge, let-it-settle, keep-the-better — over and over.

Iterated local search does this for puzzles: find a good answer by "rolling downhill" (local search), give it a small **kick**, roll downhill again, and keep the better of the two. Repeating that little nudge-and-resettle is a surprisingly powerful way to find great answers.

