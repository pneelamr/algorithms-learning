# Lin–Kernighan heuristic — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; near-optimal in practice
> **Used for:** Variable-depth edge swaps producing near-optimal tours
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

The other tidy-up methods decide *ahead of time* how many strings to cut — two, or three. Lin–Kernighan is smarter: it **doesn't decide ahead of time.**

It snips one string, then sees if pulling and re-tying suggests snipping *one more*, then maybe *one more* — following the trail of "this is making the loop shorter" as far as it keeps paying off. When the trail stops paying off, it stops and keeps the best loop it found along the way.

So instead of always cutting the same number of strings, it **figures out how deep to cut, all by itself** — which is why it untangles loops better than the fixed-cut methods.

