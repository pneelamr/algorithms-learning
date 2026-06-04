# Nearest neighbor — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Online / greedy route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; often decent but can be poor
> **Used for:** Building a quick initial tour by always going to the closest unvisited stop
> **Level 6 of 6** — researcher: tight bounds, average-case theory, online/competitive view, variants, open problems, references. See sibling files for other levels.

---

## 1. Position among constructive heuristics

Nearest neighbor is the **simplest constructive TSP heuristic** and the canonical "greedy without guarantee" object. It sits below the approximation algorithms (no constant ratio) but is the workhorse *initializer* of the entire improvement-heuristic stack:

```
Constructive heuristics (build a tour), worst-case metric ratio:
  Nearest neighbor          Θ(log n)        ← this entry (greedy next-vertex)
  Greedy edge / cheapest-link   Θ(log n)    (greedy on edges, matroid-flavored but not a tour matroid)
  Nearest / cheapest insertion  ≤ 2         (insertion family — constant ratio!)
  Farthest insertion        ≈ 2.43 (empirically excellent)
Then: 2-opt / Or-opt / Lin–Kernighan polish any of the above.
```

Notably the **insertion** heuristics achieve a *constant* ratio (≤ 2) where nearest neighbor cannot — a key theoretical contrast covered in those siblings.

## 2. Tight worst-case theory

- **Metric ratio `Θ(log n)`** (Rosenkrantz–Stearns–Lewis 1977): upper bound `½(⌈log₂ n⌉+1)`, with matching `Ω(log n)` instances. This is *asymptotically tight* — NN is provably not a constant-factor approximation, the cleanest separation between "greedy construction" and "approximation algorithm."
- **Non-metric:** no bounded ratio (NN-hard to approximate inherits TSP inapproximability).
- **Worst start vs best start:** the ratio is over the *worst* start; choosing the best of all `n` starts (repeated NN) does not break the `Ω(log n)` worst case but improves typical instances. The gap between best-start and worst-start NN is itself studied.

## 3. Average-case / probabilistic analysis

- **Euclidean BHH regime:** for `n` i.i.d. uniform points in `[0,1]²`, `OPT ≈ β√n` a.s. (Beardwood–Halton–Hammersley 1959; `β ≈ 0.7124`). Nearest neighbor's expected ratio is bounded above by a constant and empirically ~1.25; the heuristic is **constant-competitive in expectation** on random Euclidean inputs despite the `Θ(log n)` worst case. Rigorous average-case ratio bounds (constant, strictly > 1) follow from subadditive-Euclidean-functional theory (Steele; Yukich).
- This worst/average dichotomy is the standard rationale for NN's ubiquity as a seed.

## 4. The online / competitive lens

Nearest neighbor is also an **online algorithm**: when requests (cities) arrive over time and must be served immediately (online TSP / the "nearest-neighbor rule" in online routing, k-server-adjacent problems), greedy-nearest is a natural policy. Competitive-ratio analysis of greedy for online TSP/dial-a-ride and the **traveling-repairman / latency** objectives is an active area; greedy-nearest is rarely optimal-competitive but is the baseline. Connections:
- **Online metric matching / k-server:** greedy (nearest free server) is `Θ(2^k)`/`Θ(n)`-competitive — a cautionary analog of NN's myopia.
- **Online TSP** (Ausiello et al.): competitive ratios for nearest-neighbor-style and smarter policies.

## 5. Variants and modern use

- **Repeated NN, double-ended NN, greedy-endpoint** — start/endpoint diversification.
- **Space-filling-curve construction** (Hilbert/Sierpiński order): an `O(n log n)` constructor with a *constant* worst-case ratio on Euclidean instances (Platzman–Bartholdi 1989) — often preferred over NN when a guarantee is wanted cheaply.
- **Candidate-list NN** (`k`-nearest neighbor lists, quadrant/Delaunay neighbors): the standard fast constructor inside production solvers (Concorde, LKH) — NN restricted to candidate edges.
- **Learned construction:** modern neural-combinatorial-optimization "autoregressive" tour builders (Pointer Networks, Vinyals et al. 2015; attention models, Kool et al. 2019; greedy/sampling decoding) are *learned* generalizations of the nearest-neighbor "pick the next city" paradigm — the greedy decoder is literally a learned NN policy. A direct bridge from this classical heuristic to ML-for-routing.

## 6. Open problems / research threads

1. **Tight average-case constant** for NN on random Euclidean (and other) instances — improving the known bounds toward the empirical ~1.25.
2. **Competitive analysis** of nearest-neighbor policies for online/dynamic TSP, latency, and dial-a-ride variants.
3. **Learned next-city policies:** when do attention/RL constructors provably beat classical NN, and with what generalization guarantees across sizes/distributions?
4. **Smoothed analysis** of NN + 2-opt pipelines (why the seed+polish combination is fast and good in practice).
5. Best **start-selection** rules (cheaply choosing a good start without running all `n`).

NN itself is theoretically settled (`Θ(log n)` worst case, `Θ(n²)` time); its live research surface is in average/online/learned regimes and in its role as the canonical seed.

## 7. Links to related problems

- **Constructive siblings (this registry):** Greedy edge-selection (cheapest link), Nearest insertion, Cheapest insertion, Random/farthest insertion — the insertion family achieves constant ratios NN cannot.
- **Improvement partners:** 2-opt, 3-opt, Lin–Kernighan, chained LK — consume NN tours and specifically repair NN's long closing/orphan edges.
- **Approximation contrast:** MST doubling (2) and Christofides (3/2) — why a *constant* guarantee needs structure (MST/matching) beyond greedy nexting.
- **Paradigm:** greedy algorithms (and the matroid theory that explains when greedy *is* optimal — Kruskal vs NN), online algorithms / competitive analysis, neural combinatorial optimization.

## References (seminal → modern)

- Rosenkrantz, Stearns & Lewis (1977). *An analysis of several heuristics for the traveling salesman problem.* (The `Θ(log n)` NN bound; foundational.)
- Beardwood, Halton & Hammersley (1959). *The shortest path through many points.* (Euclidean `β√n` law.)
- Johnson, D. & McGeoch, L. (1997). *The traveling salesman problem: a case study in local optimization.* (Empirical NN vs others; seeds for local search.)
- Platzman, L. & Bartholdi, J. (1989). *Spacefilling curves and the planar travelling salesman problem.* (Constant-ratio `O(n log n)` constructor.)
- Steele, J. M. (1990); Yukich, J. (1998). *Subadditive Euclidean functionals* (average-case theory).
- Ausiello et al. (2001). *Algorithms for the on-line travelling salesman.* (Online/competitive.)
- Vinyals, Fortunato & Jaitly (2015). *Pointer Networks*; Kool, van Hoof & Welling (2019). *Attention, Learn to Solve Routing Problems!* (learned NN-style construction).

*Uncertainty flags:* the `Θ(log n)` worst-case ratio is fully proven; precise *average-case* constants for NN are largely empirical (~1.25 on random Euclidean) with constant-but-not-tight rigorous bounds. Competitiveness results depend on the exact online model. Claims that learned constructors beat NN are empirical and distribution-specific, without worst-case guarantees.
