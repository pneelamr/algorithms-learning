# MST doubling (tree-doubling) — Level 2: ELI10

> **Example problems:** Metric Traveling Salesman Problem, Vehicle Routing Problem, Network / route design  ·  **Type:** Approximation  ·  **Guarantee:** At most 2× optimal (metric TSP)
> **Used for:** Fast approximate routing with a worst-case guarantee
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

You want a short round trip visiting every city once. The exact methods (brute force, Held–Karp, branch-and-cut) find the *perfect* tour but can be slow. MST doubling gives up on perfection and instead promises something almost as useful: a tour that's **guaranteed to be at most twice as long as the best possible**, computed fast.

The trick starts with a **minimum spanning tree (MST)** — the cheapest set of roads that connects every city into one piece, with no loops. Think of it as the bare-minimum skeleton linking everyone. It's not a tour (a tree has dead-ends, not a single loop), but it's a brilliant starting point, because the cheapest-possible-connection can't cost more than the best tour does.

## The plain walk-through

1. **Build the MST.** Find the cheapest web of roads connecting all cities — no cycles, just enough to keep everyone linked.
2. **Double every road.** Imagine laying a second copy of each road right on top. Now every city has an *even* number of roads touching it, which means you can walk a single continuous loop that traces **every** road and returns home (this is called an "Euler tour").
3. **Walk that loop**, listing cities in the order you first reach them.
4. **Take shortcuts.** The walk revisits some cities (because of the doubled roads). Whenever the next city on the walk is one you've already visited, **skip it** and head straight to the next *new* city instead. Those shortcuts are what turn the messy walk into a clean tour that hits each city exactly once.

## Why the shortcuts don't hurt

This is the key, and it needs one fair assumption: distances are "**metric**" — going directly from A to C is never longer than detouring through B (the triangle inequality, true for real maps and straight-line distances). Because of that, every shortcut you take is **no longer** than the path it replaces. So the final tour costs no more than the full doubled-tree walk.

## Why it matters

- **A guarantee in your pocket.** Walking the doubled tree costs exactly 2× the MST, and the MST is cheaper than the best tour — so your final tour is **at most 2× optimal**, always. No nasty surprises.
- **It's fast.** Building an MST is quick even for thousands of cities, far faster than exact solving.
- **It's the foundation for better.** A famous improvement called Christofides is smarter about step 2 (it doesn't double *everything*) and tightens the guarantee from 2× to 1.5×. MST doubling is the clean, simple idea it's built on.

So MST doubling trades a little quality for a lot of speed *and* a promise: never more than twice the best. That promise — a provable worst-case bound — is exactly what makes it an "approximation algorithm" rather than just a hopeful guess.
