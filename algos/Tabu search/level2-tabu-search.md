# Tabu search — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Job-shop scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; avoids cycling
> **Used for:** Local search with memory of recent moves to avoid revisiting solutions
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The escape mechanism: memory

You've now seen several ways to escape a local optimum (a solution where every small change makes things worse): **simulated annealing** accepts worse moves randomly; **genetic algorithms** mix a population; **ant colony** learns a pheromone map. Tabu search uses a fourth idea — **memory**. It's a single-solution method (like the local searches 2-opt and Lin–Kernighan), with one twist that makes all the difference.

## The analogy, expanded

Plain local search (2-opt) only ever takes *improving* moves, so it halts the instant nothing improves — stuck. Tabu search refuses to stop there. At a local optimum it says: *"every move is worse? Fine — take the **least-bad** one and keep going."* That deliberately walks *uphill* to escape.

But there's a danger: the very next step, the best move is usually to **undo** what you just did — sliding right back into the same local optimum, forever bouncing. The fix is the **tabu list**: a short memory of your recent moves that are now **forbidden** to reverse. With the undo move banned, the search is forced onward into new territory.

One more rule keeps it sensible — the **aspiration criterion**: if a forbidden move would actually give you the *best solution you've ever seen*, allow it anyway. (Don't let a rule stop you from grabbing a record.)

## The plain walk-through

1. **Start** with a tour and an empty tabu list.
2. **Look at all neighbor moves** (e.g. all 2-opt swaps).
3. **Pick the best move that isn't tabu** — even if it makes the tour *worse* — unless a tabu move beats your all-time best (then take it: aspiration).
4. **Record that move** in the tabu list so it can't be reversed for the next few steps. (The list has a fixed length; old entries drop off — the "tabu tenure.")
5. **Track the best tour ever seen.**
6. **Repeat** for many steps; return the best.

## Why it matters

- **It never gets permanently stuck.** Because it always moves (taking the least-bad option when nothing improves) and forbids immediate backtracking, it climbs out of local optima and keeps searching — without the randomness of simulated annealing.
- **Memory = smarter exploration.** The tabu list is a *deterministic*, targeted way to avoid re-treading recent ground, rather than relying on luck.
- **Two memories, two jobs.** Short-term memory (the recent-moves list) escapes local traps; longer-term memory can **intensify** (focus on regions that have been good) or **diversify** (push toward unexplored regions). This explicit intensify/diversify control is tabu search's signature.
- **A workhorse beyond TSP.** Tabu search is especially strong on **scheduling** and **assignment** problems (job-shop, quadratic assignment), where it's often among the best methods.

The honest caveat: **no guarantee**, and it has knobs — how long moves stay tabu (the "tenure"), what exactly to forbid (a whole solution? just a move? an attribute like "edge A–B"?), and the aspiration rules. Tune them badly and it either still cycles or wanders aimlessly. On the plain TSP, Lin–Kernighan-style methods usually win; tabu search's strengths show most on scheduling and assignment. It's the **memory-based** member of the metaheuristic family; the remaining ones — **iterated local search, variable neighborhood search,** and **GRASP** — escape by *perturbation, neighborhood-switching,* and *randomized restart* instead.

