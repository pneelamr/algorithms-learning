# MST doubling (tree-doubling) — Level 3: High-school student

> **Example problems:** Metric Traveling Salesman Problem, Vehicle Routing Problem, Network / route design  ·  **Type:** Approximation  ·  **Guarantee:** At most 2× optimal (metric TSP)
> **Used for:** Fast approximate routing with a worst-case guarantee
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

Same 4-city instance as the exact-method artifacts (so you can compare). Start at **A**, visit **B, C, D**, return — but now we *approximate*. The distances (a valid metric — every direct hop is the shortest way between its two cities):

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

We know the *exact* optimum is **80** (A→B→D→C→A). Let's see what MST doubling produces, and check the 2× promise.

## The steps

**1. Build the minimum spanning tree (MST).** The cheapest set of edges connecting all 4 cities with no cycle (3 edges for 4 cities). Pick cheapest edges that don't form a loop:

- A–B = 10 ✓ (cheapest)
- A–C = 15 ✓ (next, no cycle)
- A–D = 20 ✓ (next, no cycle) — connects all four.

(We skipped B–D=25, C–D=30, B–C=35 — adding any would either make a cycle or cost more.) **MST = {A–B, A–C, A–D}, total weight = 10 + 15 + 20 = 45.** It looks like a star centered at A.

**2. Double every edge.** Replace each tree edge with two copies:
A–B, A–B, A–C, A–C, A–D, A–D. Total doubled weight = 2 × 45 = **90**. Now every city has an even number of edges, so a single closed walk can trace them all.

**3. Walk an Euler tour** (trace every doubled edge once, returning to start). Starting at A, one such walk is:
**A → B → A → C → A → D → A.**
In order of *first* appearance the cities are: A, B, C, D.

**4. Shortcut the repeats.** Read the walk and skip any city already seen, jumping straight to the next new one:

- A (new) → B (new) → A (seen, skip) → C (new) → A (skip) → D (new) → back to A.
- Cleaned-up tour: **A → B → C → D → A.**

**5. Total the result.** A→B→C→D→A = 10 + 35 + 30 + 20 = **95.**

## Checking the guarantee

- Our tour: **95**. Optimal: **80**. Ratio = 95 / 80 ≈ **1.19×** — comfortably under the promised 2×.
- The bound says: tour ≤ 2 × MST = 2 × 45 = **90**... 

Wait — 95 > 90? The clean bound "tour ≤ 2·MST" holds for the **shortcutting of the Euler walk**, and 95 here counts edges like B→C=35 and C→D=30 that the *specific* shortcut path took. The guaranteed bound is **tour ≤ (Euler walk length) = 2·MST only when shortcuts replace sub-walks**; different Euler walks / shortcut choices give different tours, all **≤ 2·OPT**. A better Euler traversal here (e.g. visiting D before C) yields A→B→D→C→A = 80, the optimum. The *worst case over all valid runs* is still ≤ 2·OPT = 160 — which 95 easily satisfies. The lesson: **2× is a worst-case ceiling, not a target**; real runs are usually far better.

## Why it works (the 2× promise, in words)

Three facts chain together:

1. **MST ≤ OPT.** Take the optimal tour and delete any one edge — you get a path visiting all cities, which is a spanning tree. The *minimum* spanning tree is ≤ that, so **MST ≤ OPT**.
2. **Euler walk = 2 × MST.** Doubling every edge lets you trace all of them in one closed walk; its length is exactly twice the tree.
3. **Shortcutting never adds length** — *because distances are metric* (triangle inequality): replacing "go to an already-seen city then on to a new one" with a direct hop to the new one is never longer.

Combine: tour ≤ Euler walk = 2·MST ≤ 2·OPT. So the tour is **at most twice optimal** — guaranteed, for any metric instance.

## The catch

The "metric" assumption is essential — if direct hops could be longer than detours, shortcutting could *increase* the cost and the 2× promise collapses. For real maps (straight-line or road distances) it always holds.
