# Greedy edge-selection (cheapest link) — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Circuit / cable layout  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; depends on instance
> **Used for:** Constructing a tour by adding cheapest valid edges first
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

Nearest neighbor builds a tour by *walking* — always stepping to the closest unvisited city from where you stand. Cheapest-link is greedy too, but it thinks about **edges (roads), not your current position.** It looks at the whole map's worth of possible roads, sorted from cheapest to most expensive, and keeps adding the cheapest one that's still "legal."

Picture all the connections you *could* build laid out as little road segments scattered across the map. You're assembling them into one big loop. You don't care where you are — you just keep snapping in the best-value piece that doesn't ruin the loop.

## The two rules that keep it legal

A finished tour is one big loop where **every city has exactly two roads** (one in, one out). So as you add cheap edges, two things would break that and must be forbidden:

1. **No city with three roads.** If a city already has two edges chosen, it's "full" — skip any new edge touching it.
2. **No early small loop.** Don't add an edge that closes a little sub-loop before *all* the cities are included. (Closing the loop is only allowed on the very last edge, when it ties everything together.)

Any edge that breaks neither rule is fair game; you always take the cheapest such edge.

## The plain walk-through

1. **Sort every possible edge** from cheapest to most expensive.
2. **Go down the list.** For each edge, ask: does adding it give some city a third road, or close a loop too early?
3. **If it's legal, add it.** If not, skip it and move on.
4. **Stop** when you've added enough edges to connect everyone into a single loop — then the final edge snaps the loop shut.

Along the way you're really building several short **chains** (paths) of cities that gradually join up, until they all merge into one closed loop.

## Why it matters

- **A different flavor of greedy.** Nearest neighbor commits to a *path you're walking*; cheapest-link commits to *globally cheap edges* wherever they are. Sometimes that global view finds bargains nearest neighbor misses — and sometimes it scatters commitments that are awkward to join up later.
- **It's a familiar idea in disguise.** This is almost exactly how you'd build a "minimum spanning tree" (the cheapest web connecting everything), but with extra rules so the result is a single *loop* instead of a branching tree.
- **Still no guarantee.** Like nearest neighbor, cheapest-link can produce poor tours on tricky maps — those last few forced edges, joining leftover chains, can be expensive. It's a fast, reasonable first draft, not a promise of quality. People often use it as a starting tour and then polish it with improvement methods.

So cheapest-link is the "shopper's" heuristic: always grab the best-priced road that doesn't break the loop, and let one big tour assemble itself from the bargains.
