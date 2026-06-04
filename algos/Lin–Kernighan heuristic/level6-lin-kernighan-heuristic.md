# Lin–Kernighan heuristic — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; near-optimal in practice
> **Used for:** Variable-depth edge swaps producing near-optimal tours
> **Level 6 of 6** — researcher: LKH and SOTA, α-nearness, the LK→Concorde link, open problems, references. See sibling files for other levels.

---

## 1. Position: the apex single-trajectory local search

Lin–Kernighan is the strongest *descent* heuristic for TSP and the engine underneath essentially every record-setting practical result. Its modern incarnation, **LKH** (Keld Helsgaun), and its restart wrapper, **chained/iterated LK**, are what produce the best known tours on instances from thousands to **tens of millions** of cities, and the **upper bounds** that drive the exact **Concorde** branch-and-cut solver's pruning. LK is where the heuristic and exact clusters meet.

## 2. From LK to LKH: what changed

- **α-nearness candidate lists** (Helsgaun 2000): edges ranked by their **Held–Karp 1-tree** cost increase `α(i,j)`, optimized over Lagrangian (subgradient) `π`-penalties. Dramatically better candidate sets than k-nearest; the principal source of LKH's edge over classic LK and a direct import of the **Held–Karp 1-tree lower bound** (see Held–Karp / branch-and-bound entries).
- **Sequential 5-opt base move** (Helsgaun 2009, LKH-2): the elementary move is a sequential 5-opt step (not 2-/3-opt), with general `k`-opt submoves; markedly deeper effective neighborhood.
- **Partitioning & merging** (LKH-2): tour partitioning, backbone/`POPMUSIC` initialization, and merging of independent runs for very large instances.
- **Restart / perturbation** (chained LK; LKH's `kicks`): double-bridge perturbations between LK descents — the subject of this cluster's final entry.

## 3. Theory frontier

- **No approximation guarantee**; LK local optima have no proven constant ratio. Empirically plain LK ≈ 1–2% over optimal; LKH with restarts reaches **< 0.1%** and frequently *optimal* on TSPLIB.
- **The LK lemma** (positive partial sums) underpins greedy depth control; tight characterizations of *which* `λ`-opt moves LK's sequential restriction misses (beyond the double bridge) remain incompletely mapped.
- **PLS / convergence.** As a k-opt-class local search, LK inherits PLS-hardness; no polynomial bound on descent length. Smoothed analysis for LK (cf. the 2-opt results) is largely open.
- **Lower-bound coupling.** α-nearness ties heuristic candidate quality to the **Held–Karp bound**; the empirical near-optimality of LKH and the strength of the Held–Karp bound are deeply linked (the bound is typically within ~0.7% of OPT).

## 4. SOTA and ecosystem

- **LKH-2 / LKH-3** (Helsgaun): the reference implementations; LKH-3 extends to constrained variants (VRP, CVRP, PDP, TSPTW, etc.).
- **Concorde** uses LK-class heuristics for primal upper bounds feeding **branch-and-cut** (exact-methods cluster).
- **Learned LK:** neural candidate generation / learned policies guiding LK moves (2019–), and GNN edge-scorers replacing or augmenting α-nearness — promising but not yet beating LKH on hard benchmarks at scale.
- **Records:** LKH has produced best-known tours on the largest TSPLIB and World-TSP (1.9M, ~7.5M cities) instances, within tiny fractions of a percent of the Held–Karp bound.

## 5. Open problems / threads

1. **Provable guarantees** for any LK-class local optimum on metric/Euclidean inputs (even average-case constants).
2. **Smoothed complexity of LK** analogous to Englert–Röglin–Vöcking for 2-opt.
3. **Learned candidate lists** that provably/empirically beat α-nearness across distributions and scales.
4. **Characterizing the sequential blind spot:** which improving `λ`-opt moves are systematically unreachable, and how much they cost.
5. **Parallel/distributed LK** with quality-preserving partitioning for `10⁸`+ cities.

## 6. References (seminal → modern)

- Lin, S. & Kernighan, B. (1973). *An effective heuristic algorithm for the traveling-salesman problem.* (The method and the gain lemma.)
- Helsgaun, K. (2000). *An effective implementation of the Lin–Kernighan TSP heuristic.* (α-nearness, LKH.)
- Helsgaun, K. (2009). *General k-opt submoves for the Lin–Kernighan TSP heuristic.* (LKH-2, sequential 5-opt.)
- Johnson & McGeoch (1997). *The TSP: a case study in local optimization.* (LK empirical analysis, `O(n^{2.2})`.)
- Applegate, Bixby, Chvátal & Cook (2006). *The Traveling Salesman Problem: A Computational Study.* (Concorde; LK as upper-bound engine.)
- Held & Karp (1970/71). *The traveling-salesman problem and minimum spanning trees* (I/II). (The 1-tree bound behind α-nearness.)

## 7. Links to related problems

- **Down the ladder:** 2-opt (LK depth-1), 3-opt (fixed-depth predecessors).
- **Up:** chained Lin–Kernighan (double-bridge restarts — the non-sequential move LK can't make).
- **Cross-cluster:** Held–Karp 1-tree bound → α-nearness; Concorde branch-and-cut consumes LK upper bounds; Held–Karp DP and branch-and-bound supply the exact backdrop.
- **Forward to metaheuristics:** LK is the inner engine for iterated local search; simulated annealing, tabu search, GRASP, and VNS are alternative *outer* strategies wrapping a local-search core.

*Uncertainty flags:* the 1–2% (LK) and <0.1% (LKH+restarts) gaps are empirical and instance-dependent (TSPLIB/Euclidean). α-nearness's superiority is empirically robust but not formally guaranteed. "Within ~0.7% of OPT" for the Held–Karp bound is an empirical regularity on geometric instances, not a theorem.

