# Chained Lin–Kernighan — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Large-scale Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; often extremely close to optimal
> **Used for:** Restarting Lin–Kernighan from perturbed tours for large-scale, very-high-quality solving
> **Level 6 of 6** — researcher: SOTA, the big-valley landscape, the exact-solver coupling, open problems, references. See sibling files for other levels.

---

## 1. Position: the practical state of the art

Chained / iterated Lin–Kernighan is the strongest general-purpose TSP tour finder in use. It is the production engine of **LKH** and the upper-bound generator inside **Concorde**, and it is responsible for essentially every best-known tour on large benchmark and World-TSP instances. It is also the archetype of **iterated local search (ILS)** — the cleanest demonstration that a strong descent method plus a well-chosen *escape* move beats both the descent alone and naive restarts. As the final entry in this cluster, it marks the transition from *neighborhood* local search to the *metaheuristic* strategies of the next cluster.

## 2. The big-valley hypothesis

The empirical foundation: for Euclidean TSP, local optima are not scattered uniformly — they form a **"big valley,"** a globally convex-ish landscape in which better local optima lie closer (in shared-edge distance) to the global optimum and to each other. This structure is *why* chained LK works:
- Good local optima share a large **backbone** of edges; the double bridge preserves backbone while reshuffling, so the walk on `S*` drifts down the valley.
- It also predicts diminishing returns and motivates **backbone-guided** and **tour-merging** methods (Cook–Seymour) that exploit the valley structure more aggressively than blind kicking.

Characterizing the big valley rigorously (beyond empirics) remains a live research thread linking landscape theory, the TSP polytope, and the Held–Karp bound.

## 3. Lineage and SOTA variants

- **Large-step Markov chains** (Martin, Otto & Felten 1991): the original "kick + LK + Metropolis" formulation; introduced the double bridge as the perturbation.
- **"Chained Lin–Kernighan"** (Applegate, Cook & Rohe 2003; Applegate–Bixby–Chvátal–Cook, *Computational Study* 2006): the name, the engineering for scale (don't-look-bit warm restarts, two-level tour structures), and integration into **Concorde**.
- **LKH-2/3** (Helsgaun): chained LK over sequential **5-opt** moves with **α-nearness** candidate lists (Held–Karp 1-tree), partitioning, and run-merging — the reference high-quality solver, scaling to `~10⁷`–`10⁸` cities.
- **Tour merging / backbone** (Cook & Seymour 2003): combine many chained-LK tours into a sparse graph and solve/optimize over it — pushing closer to optimal than any single ILS run.
- **Learned perturbation / acceptance:** RL-guided kick selection and neural ILS controllers (2020s) — promising, not yet dominating LKH at scale.

## 4. The exact-solver coupling (closing the loop)

Chained LK is where this registry's **heuristic** and **exact** clusters meet operationally:
- It produces a tight **primal upper bound** `UB`.
- The **Held–Karp / 1-tree Lagrangian bound** and the **branch-and-cut** LP relaxation produce a **lower bound** `LB`.
- **Concorde** prunes branch-and-cut nodes whose `LB ≥ UB`; a near-optimal chained-LK `UB` shrinks the search tree dramatically, often making *proof of optimality* feasible. So the best heuristic accelerates the best exact method — the practical payoff of the whole TSP toolchain.

## 5. Open problems / threads

1. **Landscape theory:** a rigorous account of the big valley and provable guarantees for ILS/chained LK on Euclidean inputs.
2. **Optimal perturbation theory:** principled (not just empirical) choice of kick strength/locality as a function of `n` and instance structure; adaptive/portfolio kicks.
3. **Acceptance & restart schedules** with provable mixing on the local-optima graph (the large-step Markov chain analyzed rigorously).
4. **Learned ILS controllers** that generalize across distributions and beat hand-tuned LKH at scale.
5. **Parallel/distributed chained LK + tour merging** for `10⁸`+ cities with quality guarantees.

## 6. References (seminal → modern)

- Martin, Otto & Felten (1991). *Large-step Markov chains for the traveling salesman problem.* (Kick + LK + Metropolis; the double bridge.)
- Lin & Kernighan (1973). *An effective heuristic algorithm for the TSP.* (The base local search.)
- Applegate, Cook & Rohe (2003). *Chained Lin–Kernighan for large traveling salesman problems.* (The method, named and engineered.)
- Applegate, Bixby, Chvátal & Cook (2006). *The Traveling Salesman Problem: A Computational Study.* (Concorde; chained LK as upper-bound engine.)
- Helsgaun (2000, 2009). *LKH* and *General k-opt submoves.* (α-nearness, 5-opt, restarts at scale.)
- Cook & Seymour (2003). *Tour merging via branch-decomposition.* (Backbone/tour-merging beyond single ILS runs.)
- Lourenço, Martin & Stützle (2003/2019). *Iterated Local Search.* (The general framework chained LK instantiates.)

## 7. Links to related problems

- **Inside it:** Lin–Kernighan (the engine), 2-opt / 3-opt (its predecessors; the double bridge is the 4-opt move none of them can make).
- **Cross-cluster (exact):** Held–Karp 1-tree bound → α-nearness + lower bound; **branch-and-cut / Concorde** consumes chained-LK upper bounds; Held–Karp DP and branch-and-bound complete the exact backdrop.
- **Forward (metaheuristics):** chained LK is **iterated local search** specialized to TSP; **simulated annealing** (Metropolis on single moves), **tabu search** (memory-based escape), **GRASP** (randomized-greedy restarts), **VNS** (systematic neighborhood change), and **genetic / ant-colony** methods are sibling answers to the local-optimum-escape problem — the next cluster.

*Uncertainty flags:* the <0.1%/optimal-on-TSPLIB and `10⁷`–`10⁸`-city scaling figures are empirical (Applegate et al.; Helsgaun) and instance/hardware dependent. The big-valley hypothesis is an empirical regularity with partial theory. The Held–Karp-bound sandwich (~0.7% gap) is an empirical geometric-instance regularity, not a theorem. "Minimal move outside LK's neighborhood" is the standard characterization of the double bridge, not a formal minimality proof for all LK variants.

