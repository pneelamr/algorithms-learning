# Variable neighborhood search — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Clustering / p-median  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; systematic neighborhood changes
> **Used for:** Systematically switching neighborhood structures to balance diversify/intensify
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The core fact this is built on

A tour can be a **local optimum for one kind of move but not another.** Here's the cleanest example, reusing the idea from the 3-opt artifact.

Take a 6-city tour where city **X** is stranded in the wrong place:

**1 – 2 – X – 3 – 4 – 5 – 1**, and X really belongs between 4 and 5.

Suppose **no 2-opt move improves this tour** — every single-segment reversal that pulls X out also flips a good chunk of the route the wrong way, so under the **2-opt neighborhood** the tour is **stuck** at, say, length 100.

But under a **different neighborhood — "relocate one city" (Or-opt)** — there's an easy win: **pick up X and drop it between 4 and 5**:

**1 – 2 – 3 – 4 – X – 5 – 1**

- Removed: edges `2–X`, `X–3`, `4–5`
- Added: edges `2–3`, `4–X`, `X–5`

With X genuinely belonging by 4 and 5, the added edges are short and the removed ones long — say the tour drops from 100 to **80**. **The same tour that 2-opt called "finished" was trivially improvable by a relocate move.** That is exactly the fact VNS exploits.

## How VNS uses it

VNS keeps a **ladder of neighborhoods** and switches between them. A simple ladder for tours:
- `N₁` = swap two cities
- `N₂` = reverse a segment (2-opt)
- `N₃` = relocate a chunk (Or-opt)
- `N₄` = double-bridge-sized shuffle

When stuck in a small neighborhood, **shake** with a bigger one, then **local-search** back down, and **reset to `N₁` whenever you improve.**

## A worked cycle

**Start:** the stranded-X tour, 2-opt-optimal at **100**. Best = 100. `k = 1`.

1. **Shake in `N₁`** (random city swap): swap two cities → a different tour, say length 112. **Local search** (2-opt) from there → back to **100** (no improvement). Not better → **`k = 2`.**
2. **Shake in `N₂`** (random 2-opt move) → 108 → local search → **100** again. Not better → **`k = 3`.**
3. **Shake in `N₃`** (relocate a chunk — this is the move that helps!): the shake relocates X near 4–5, giving a tour that local search then tidies to **80**. `80 < 100` → **better!** Adopt 80, **reset `k = 1`** and search carefully around the new best.

The ladder climbed until it reached the *kind* of move (relocation) that could escape the trap — then snapped back to careful local search around the improved tour.

> Why not the shared 4-city instance? With 4 cities, 2-opt already reaches the optimum (80) — there's no trap where one neighborhood is stuck but another isn't. You need at least the 6-city stranded-city setup above to see why *changing the neighborhood* matters.

## The numbered recipe

1. **Define** a ladder `N₁, N₂, …, N_max` (small → large moves) and a starting tour; run local search.
2. **`k = 1`.**
3. **Shake:** pick a random neighbor in `Nₖ`.
4. **Local search** from it to a local optimum.
5. **If better than best:** adopt, **reset `k = 1`.** **Else:** `k ← k + 1` (wrap to 1 if past the top).
6. **Repeat** until the budget ends; return the best.

## Why it works

- **Being "stuck" is neighborhood-specific.** Switching the kind of move is a *systematic* way to find one that escapes the current trap — more principled than relying on randomness.
- **Climb when stuck, reset when you win.** Growing `k` makes the shakes bolder until something breaks the deadlock; resetting to `N₁` after any improvement re-focuses the careful search on the new good region. That's automatic diversify-then-intensify.
- **Local search does the polishing.** The shake only needs to *escape*; the local search turns the rough shaken tour back into a clean local optimum.

## The catch

**No guarantee**, and the **ladder design** matters a lot — which neighborhoods, in what order. A good ladder (mixing reversal, relocation, and bigger shuffles) escapes many trap types; a poor one wastes effort. VNS is very close to **iterated local search** — both shake then re-optimize — the difference being VNS **grows the shake size systematically** while ILS usually uses one fixed kick (the double bridge). VNS is the **neighborhood-changing** escape; **tabu search** (memory) and **GRASP** (randomized restart) are the remaining single-solution siblings.

