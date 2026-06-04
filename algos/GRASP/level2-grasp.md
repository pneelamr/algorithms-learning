# GRASP — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Set covering, Scheduling  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; multi-start greedy + local search
> **Used for:** Multi-start randomized-greedy construction followed by local search
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The escape mechanism: many fresh starts

GRASP stands for **Greedy Randomized Adaptive Search Procedure**. Its way of not getting stuck is the simplest of all the metaheuristics: **start over, many times, from different good-but-random beginnings.** Each restart builds a brand-new solution and polishes it; you keep the best across all restarts. No memory (unlike tabu search), no kicking an existing solution (unlike iterated local search) — just **independent tries**, each one smart enough to be worth polishing.

## Two halves of every round

Each GRASP iteration has two phases:

**1. Greedy randomized construction.** Build a solution from scratch, one piece at a time. At each step:
- Look at all the choices and score them (e.g., for a tour, "how short is the next hop?").
- Make a short list of the **best few** choices — the **Restricted Candidate List (RCL)**.
- Pick **one at random** from that list (not always the single best).

Pure greedy (always the single best) makes the *same* solution every time. Pure random makes *bad* solutions. The RCL is the dial in between: good choices, but with enough randomness that every restart is **different**. ("Adaptive" means the scores are recomputed each step as the solution grows.)

**2. Local search.** Take that freshly-built solution and **tidy it** to a local optimum (e.g. 2-opt for tours). Construction gets you a good *starting* point; local search squeezes out the easy improvements.

Then: repeat from phase 1 with new random luck, and **remember the best** solution seen.

## The plain walk-through

1. **Repeat** many times:
   a. **Construct** a solution greedily-but-randomly (using the RCL).
   b. **Local-search** it to a local optimum.
   c. **Keep it if it's the best so far.**
2. Return the best.

## Why it matters

- **Dead simple, surprisingly good.** Two easy ingredients — a randomized greedy builder and a local search — combine into a solid, reliable method. It's one of the easiest metaheuristics to implement correctly.
- **Diversity by construction.** Because each restart *builds* a different solution, GRASP naturally covers many regions of the search space — the multi-start version of the "many diverse seeds" idea you saw with random insertion.
- **Trivially parallel.** Every restart is independent, so you can run them all at once on many cores.
- **Broad reach.** Anywhere you have a greedy heuristic and a local search — TSP, **set covering, scheduling**, assignment — you can build a GRASP.

The honest caveat: **no guarantee**, and because the restarts are **independent**, GRASP "forgets" everything between rounds — it doesn't *learn* from good past solutions the way ant colony does, or *reuse* a good solution the way iterated local search does. The usual upgrade, **path relinking**, adds a memory that connects good past solutions, fixing exactly this. On the plain TSP, Lin–Kernighan-style methods usually win; GRASP's strengths are simplicity, parallelism, and breadth. It's the **randomized-restart** escape — the last of the single-solution family, alongside **tabu search** (memory), **iterated local search** (perturbation), and **variable neighborhood search** (neighborhood-switching).

