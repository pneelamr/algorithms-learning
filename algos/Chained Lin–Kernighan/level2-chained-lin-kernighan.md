# Chained Lin–Kernighan — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Large-scale Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; often extremely close to optimal
> **Used for:** Restarting Lin–Kernighan from perturbed tours for large-scale, very-high-quality solving
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The problem this fixes

Lin–Kernighan (LK) makes excellent tours, but it eventually reaches a **local optimum**: a tour it can't improve with any of its chain-moves, even though a better tour exists somewhere nearby. Restarting LK from a brand-new random tour usually just wastes the good structure you already found. Chained LK threads the needle: **keep the good tour, but jolt it just enough to break free, then re-optimize.**

## The key trick: the double-bridge "kick"

There's a special move called a **double bridge**:
1. Cut the tour in **four** places, splitting it into four chunks in order: **A, B, C, D**.
2. Reconnect them in the **swapped order A, C, B, D**.

Two things make this the perfect "kick":
- **LK can't undo it in one move.** The way LK builds its chains, it literally cannot make (or un-make) a double bridge directly — so the kick genuinely moves you to a new area instead of being instantly reversed.
- **It barely damages the tour.** It keeps all four chunks intact and only re-orders them, so you're close to where you were — LK can re-tidy quickly, often into something *better*.

## The plain walk-through

1. **Start:** make a tour and run LK on it → a polished local-optimum tour, your current **best**.
2. **Kick:** apply a double bridge to the best tour → a slightly scrambled tour.
3. **Re-optimize:** run LK on the scrambled tour → a new polished tour.
4. **Keep the better:** if the new tour beats your best, adopt it; otherwise throw it away and keep the old best.
5. **Repeat** steps 2–4 — hundreds, thousands, millions of times.

This pattern — *take a good solution, perturb it, re-optimize, keep the better* — is a famous general strategy called **iterated local search.** Chained LK is that strategy with LK as the optimizer and the double bridge as the perturbation.

## Why it matters

- **The best tours people can find.** Chained LK / LKH routinely gets within a **fraction of a percent** of the true optimum, and often finds the optimum outright, on huge maps.
- **It scales.** Because each kick only disturbs a small part of a big tour, you can run this on instances with **millions of cities.**
- **It powers the exact solvers too.** The world-class *exact* TSP solver (Concorde) uses chained-LK tours as its "best guess so far," which helps it prove optimality faster.

The honest caveat: it **still** has no mathematical guarantee — it's just extremely good in practice — and it needs the LK engine plus careful choices (how hard to kick, when to accept a worse tour to keep exploring). It's the **last and strongest** method in this local-search cluster, and the bridge to the next family — *metaheuristics* like simulated annealing and tabu search, which are different answers to the same question: **how do you escape a local optimum?**

