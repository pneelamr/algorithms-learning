# 2-opt local search — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; big practical improvement
> **Used for:** Improving a tour by uncrossing pairs of edges until no swap helps
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## A different kind of method

Everything before this — nearest neighbor, cheapest-link, the insertion family — **builds** a tour from nothing. 2-opt is the first method here that does the opposite job: you hand it a **finished tour** (built by any of those), and it **polishes** it. We call these *improvement* (or *local search*) heuristics. The usual recipe is: a constructor makes a quick rough tour, then 2-opt cleans it up.

## The analogy, expanded

Picture your tour as a loop of road on a map. Sometimes the loop **crosses itself** — two stretches of road make an X. Whenever that happens, the tour is wasting distance, and there's always a way to reconnect those two stretches *without* the crossing that makes the loop shorter.

2-opt's whole move is:
1. **Pick two edges** of the tour (two road segments).
2. **Delete them**, which splits the loop into two paths.
3. **Reconnect the only other way** that makes a single loop again — this flips one of the paths around.
4. If the new loop is **shorter**, keep it. Otherwise put it back.

Do this over and over. When you can't find *any* pair of edges whose swap helps, you stop — the tour is now **"2-optimal."**

## The plain walk-through

1. **Start with a tour** — anything: nearest-neighbor output, a random order, whatever.
2. **Scan pairs of edges.** For each pair, compute how much length you'd save by deleting them and reconnecting the other way.
3. **Make improving swaps.** Whenever a swap shortens the tour, do it.
4. **Repeat** until a full scan finds no improving swap. Done.

The magic of why a swap is so simple: once you delete two edges from a loop, there is **exactly one** other way to stitch the pieces back into a single loop. So you're never confused about *how* to reconnect — only *which two edges* to cut.

## Why it matters

- **It fixes the obvious mistakes cheaply.** Constructors like nearest neighbor leave behind silly crossings and one big "stranded city" jump. 2-opt erases exactly those.
- **It works on any starting tour.** Bad seed or good seed, 2-opt makes it better.
- **It's the foundation.** 2-opt is the simplest member of a family (3-opt, Lin–Kernighan, chained Lin–Kernighan) that goes on to produce the best tours people know how to find. Understand 2-opt and the rest are "the same idea, but cut in more places."

The honest caveat: 2-opt gives **no guarantee** about how close to perfect it gets, and it stops at a "no single swap helps" tour (a *local optimum*) that might not be the true best — there can be a better tour you can only reach by changing several edges at once. That limitation is exactly what the next methods in this cluster are built to overcome.

