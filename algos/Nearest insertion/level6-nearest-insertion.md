# Nearest insertion — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Drilling / pick-path planning  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by inserting the nearest outside node at lowest cost
> **Level 6 of 6** — researcher: the insertion family's bounds, average-case, variants, open problems, references. See sibling files for other levels.

---

## 1. Position: the constant-ratio constructive tier

Nearest insertion is the entry point to the **insertion family**, the constructive heuristics that — unlike nearest neighbor and greedy-edge (`Θ(log n)`) — achieve **constant** metric approximation ratios by maintaining a sub-tour and growing it via cheapest placement:

```
Constructive heuristic        metric worst-case ratio       empirical (rand. Euclidean)
  Nearest neighbor            Θ(log n)                       ~25% over OPT
  Greedy-edge / multi-fragment Θ(log n)                      ~10–15%
  Nearest insertion           ≤ 2 (tight)                    ~20%          ← this entry
  Cheapest insertion          ≤ 2                            ~15–20%
  Farthest insertion          ≤ 2·⌈log n⌉ +0.16... (RSL) but ≈2.43-flavored; best empirically  ~10–15%
  Random insertion            O(log n) expected bound; very strong empirically  ~10–15%
  Christofides (approx.)       3/2                            ~10%
```

The insertion heuristics and MST doubling share the **same 2 bound via the MST**, but insertion keeps a feasible tour throughout, making it a more natural *incremental/online* constructor.

## 2. The insertion family's worst-case landscape (RSL 1977)

Rosenkrantz, Stearns & Lewis established the canonical bounds, all for metric TSP:
- **Nearest insertion ≤ 2**, tight.
- **Cheapest insertion ≤ 2**, tight (selection = vertex of minimum insertion cost; intuitively also MST-linked).
- **Farthest insertion:** RSL prove an upper bound of `⌈log n⌉ + 0.16` -ish in their framework, yet it is **empirically the best** of the family — a striking worst-case/average-case inversion. Inserting the *farthest* vertex first quickly fixes the tour's global "outline" (a convex-hull-like skeleton), so subsequent insertions only do local refinement.
- **Random insertion:** strong in practice; the subject of the sharpest modern analysis (below).

The non-obvious lesson: a *better worst-case constant* (nearest/cheapest, 2) does **not** imply *better tours* (farthest/random usually win). Selection-rule design trades provable ceiling against typical-case skeleton quality.

## 3. Average-case theory and the random-insertion frontier

- On random Euclidean instances (`OPT ≈ β√n`, BHH 1959), all insertion heuristics are constant-competitive in expectation; precise constants are mostly empirical.
- **Random insertion** has attracted rigorous attention: it is provably `O(log n / log log n)` or better in various models, and a line of work (e.g. analyses showing random insertion is within a small constant of optimal on structured inputs) keeps tightening this. Random/ farthest insertion are the family members researchers most often single out for surprisingly good guarantees relative to their simplicity.
- **Convex-position / planar structure:** for points in convex position the optimal tour is the convex-hull order; insertion heuristics that respect hull structure (farthest, and "greatest-angle" insertion) exploit this, connecting to computational-geometry analyses.

## 4. Variants and applications

- **Convex-hull insertion** (Stewart; Golden et al.): initialize the sub-tour with the **convex hull** of the points, then insert interior points by cheapest (or largest-angle) placement — a classic strong Euclidean constructor building on the insertion paradigm.
- **Or-insertion / segment insertion:** insert short *chains* rather than single vertices (kin to Or-opt moves), useful in VRP.
- **VRP & pick-path:** insertion heuristics (esp. **cheapest/parallel insertion**, Solomon's I1 for VRP with time windows) are workhorses in vehicle routing and PCB-drilling / warehouse pick-path planning — the "grow feasible routes by cheapest insertion" template generalizes directly.
- **Online TSP:** the sub-tour-growing structure is naturally incremental — insertion is a sensible policy when points arrive over time.

## 5. Open problems / research threads

1. **Tight average-case constants** for nearest/cheapest/farthest/random insertion on random Euclidean (and other random) metrics — closing the gap between empirical ~1.1–1.25 and rigorous bounds.
2. **Explain the worst/average inversion:** rigorous account of why farthest insertion (weak worst case) beats nearest insertion (tight 2) on typical inputs — partial answers via skeleton/convex-outline arguments remain incomplete.
3. **Sharper random-insertion bounds** and the best provable simple constructor.
4. **Learned selection policies:** RL/GNN-chosen insertion order generalizing the nearest/farthest rules; guarantees and cross-distribution generalization open.
5. Insertion + local-search **smoothed analysis** (why insertion seeds give good 2-opt/LK basins).

Nearest insertion itself is settled (tight 2, `Θ(n²)`, MST-proof); its frontier lies in the family's average-case theory and the surprising strength of its farthest/random cousins.

## 6. Links to related problems

- **Insertion-family siblings (this registry):** Cheapest insertion (≤ 2, selection by insertion cost), Random / farthest insertion (different constants; farthest empirically best).
- **Shared 2-bound cousin:** MST doubling (also 2 via the MST; nearest insertion's connection distances *are* the MST by the Prim correspondence). Christofides (3/2) is the next step up in guarantee.
- **Greedy contrast:** nearest neighbor & greedy-edge (`Θ(log n)`) — insertion's constant bound is the payoff for keeping a feasible sub-tour.
- **Improvement partners:** 2-opt, Or-opt, Lin–Kernighan consume insertion tours as (often superior) seeds.
- **Paradigm & tooling:** Prim's MST, convex hull, subadditive Euclidean functionals (average-case), Clarke–Wright / Solomon insertion for VRP.

## References (seminal → modern)

- Rosenkrantz, Stearns & Lewis (1977). *An analysis of several heuristics for the traveling salesman problem.* (The insertion-family bounds: nearest/cheapest ≤ 2, farthest, etc.)
- Beardwood, Halton & Hammersley (1959). *The shortest path through many points.* (Euclidean `β√n` law.)
- Golden, Bodin, Doyle & Stewart (1980). *Approximate traveling salesman algorithms* (convex-hull and insertion comparisons).
- Stewart, W. (1977). *A computationally simple heuristic for the TSP* (convex-hull insertion).
- Johnson, D. & McGeoch, L. (1997). *The traveling salesman problem: a case study in local optimization.* (Empirical insertion comparisons; farthest best.)
- Solomon, M. (1987). *Algorithms for the vehicle routing and scheduling problems with time window constraints* (insertion heuristics for VRP).
- (Modern) analyses of **random insertion** for TSP (e.g. competitive/average-case bounds, 2010s).

*Uncertainty flags:* the worst-case constants (nearest/cheapest ≤ 2, tight) are proven; *average-case* constants are largely empirical. The "farthest insertion is best in practice" claim is empirical and distribution-dependent (Johnson–McGeoch). Exact figures in the comparison table are representative, not universal. Random-insertion's sharpest bounds depend on the input model.
