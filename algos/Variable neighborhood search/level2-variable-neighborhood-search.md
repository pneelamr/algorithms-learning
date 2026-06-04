# Variable neighborhood search — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Clustering / p-median  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; systematic neighborhood changes
> **Used for:** Systematically switching neighborhood structures to balance diversify/intensify
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The escape mechanism: change the neighborhood

A "neighborhood" is the set of small changes a local search is allowed to make — for tours, 2-opt's neighborhood is "all single-segment reversals." A local optimum is only stuck *relative to one neighborhood*. The key insight behind variable neighborhood search (VNS):

> **A solution that's stuck for one kind of move may be easily improvable by a *different* kind of move.**

So instead of escaping by random worse moves (simulated annealing), memory (tabu search), or a fixed kick (iterated local search), VNS escapes by **systematically switching which neighborhood it uses.**

## The analogy, expanded

Keep a **ladder of neighborhoods**, usually ordered small → large. For tours that might be: `N₁` = swap two cities, `N₂` = reverse a segment (2-opt), `N₃` = relocate a chunk (Or-opt), `N₄` = a double-bridge-sized shuffle, …

VNS works the ladder like this:
1. **Shake** — make a random move in the current neighborhood `Nₖ` (a bigger `k` = a bigger jump). This throws you to a new nearby point.
2. **Local search** — tidy up from there (often with a small neighborhood) to a local optimum.
3. **Decide** — if it's better than your best, **keep it and drop back to the smallest neighborhood `N₁`** (search carefully around the new good spot). If it's not better, **move up to the next-bigger neighborhood `Nₖ₊₁`** (jump farther next time).

So: found something good? Look carefully nearby. Stuck? Widen the search. That simple rule of climbing the neighborhood ladder when stuck and resetting when you improve is the whole method.

## The plain walk-through

1. **Set up** a ladder of neighborhoods `N₁ ⊂ N₂ ⊂ … ⊂ N_max` (small to large) and a starting solution.
2. **`k = 1`.**
3. **Shake:** pick a random solution in `Nₖ` of the current one.
4. **Local search** from it to a local optimum.
5. **If better:** adopt it, **reset `k = 1`.** **Else:** `k = k + 1` (try a bigger neighborhood). If `k` passes the top, loop back to `k = 1`.
6. **Repeat** until the time budget runs out; return the best.

## Why it matters

- **Different moves unstick different traps.** Because being stuck depends on the neighborhood, switching neighborhoods is a principled, *systematic* way to escape — not relying on luck or randomness.
- **Automatic zoom.** Improving ⇒ zoom in (small neighborhood, careful search); stuck ⇒ zoom out (big neighborhood, bold jump). The method balances **intensify** and **diversify** just by climbing and resetting the ladder.
- **Simple and very general.** It needs only a set of neighborhoods and a local search, so it adapts to routing, **clustering/facility-location (p-median)**, and many others — where it's frequently a top performer.

The honest caveat: **no guarantee**, and you must **design the neighborhood ladder** (which moves, in what order) — a good ladder makes VNS shine, a poor one wastes effort. It's closely related to **iterated local search** (both shake-then-reoptimize), the difference being that VNS *systematically grows the shake size* while ILS usually uses one fixed kick. VNS is the **neighborhood-changing** escape; its siblings are **tabu search** (memory), **iterated local search** (fixed perturbation), and **GRASP** (randomized restart).

