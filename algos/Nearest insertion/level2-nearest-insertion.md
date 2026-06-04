# Nearest insertion — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Drilling / pick-path planning  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by inserting the nearest outside node at lowest cost
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

Nearest neighbor and cheapest-link both build a tour by stretching out a *path*, one hop or one edge at a time. Insertion methods work differently and, it turns out, better: they keep a **complete little loop at every step** and grow it outward.

Picture a loop of string holding a few cities. To add a new city you do two things:
1. **Pick which city to add:** the one that sits **closest to the loop** you've built so far.
2. **Pick where to add it:** find the single edge of the loop where slipping that city in adds the **least extra length** — snip that edge and tie the city in between its two endpoints.

Because you always keep a real loop and only ever expand it a tiny bit, the tour stays tidy and "round" instead of zig-zagging.

## The two questions, named

- **Which node?** — *Selection.* Nearest insertion picks the outside city nearest to *any* city already in the loop.
- **Where to put it?** — *Placement.* Wherever it's cheapest: insert city `k` between neighbors `i` and `j` to minimize the extra cost `d(i,k) + d(k,j) − d(i,j)` (the length the loop grows by).

Different insertion heuristics change only the *which-node* rule (nearest, cheapest, farthest, random); the *where* rule is always "cheapest place."

## The plain walk-through

1. **Start tiny.** Begin with a 2-city loop (two cities and the edge back and forth), often the closest pair.
2. **Find the nearest outsider.** Among all cities not yet in the loop, pick the one closest to the loop.
3. **Insert it cheaply.** Try every edge of the loop; slot the new city into the one that grows the loop the least.
4. **Repeat** until every city is in the loop.

Each round the loop gets one city bigger, but always stays a single closed tour.

## Why it matters

- **A real guarantee, at last.** The earlier greedy builders (nearest neighbor, cheapest-link) can be a *growing* factor worse than the best tour as cities pile up. Nearest insertion (on normal "map-like" distances) is provably **never more than twice the best possible** — a fixed promise that doesn't degrade with more cities. That's a meaningful step up.
- **Tidier tours.** Growing a loop outward naturally avoids the long "stranded city" jumps that plague path-growing methods, so the tours look and score better.
- **Still simple and fast.** It's just "find nearest, insert cheapest," repeated — easy to code and quick to run, and a great starting tour for polishing methods afterward.

The honest caveat: "twice the best" is a worst-case ceiling, and it needs distances to behave like real maps (a direct hop never longer than a detour). In everyday practice nearest insertion lands much closer to optimal — and it's the gentle introduction to a whole family (cheapest, farthest, random insertion) that trades "which city next?" rules for different strengths.
