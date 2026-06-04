# Cheapest insertion — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Logistics route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by always making the globally cheapest insertion
> **Level 6 of 6** — researcher: the family's bounds, average-case, variants, open problems, references. See sibling files for other levels.

---

## 1. Position within the insertion family

Cheapest insertion is the **joint-minimization** member of the constructive insertion family: its selection rule is defined by the placement objective, so each step is the globally minimal tour-length increase. Among the family it is the "textbook greedy" baseline, sharing the **tight metric 2-bound** with nearest insertion but reaching it by *domination* rather than the Prim identity:

```
Constructive heuristic        metric worst-case ratio       empirical (rand. Euclidean)
  Nearest insertion           ≤ 2 (tight)                    ~20%
  Cheapest insertion          ≤ 2 (tight)                    ~15–20%      ← this entry
  Farthest insertion          no constant proven; O(log n) generic  ~10–15% (best of family)
  Random insertion            O(log n)-type expected; strong  ~10–15%
  Christofides (approx.)       3/2                            ~10%
```

The recurring family lesson it sharpens: a *better/identical worst-case constant* (cheapest/nearest, 2) does **not** imply better tours — farthest and random insertion, with no better (or weaker) provable constant, win empirically. Selection-rule design trades provable ceiling against typical-case skeleton quality.

## 2. Worst-case landscape (RSL 1977)

Rosenkrantz, Stearns & Lewis established the canonical metric bounds:
- **Nearest insertion ≤ 2**, tight (Prim-faithful; connection distances *are* the MST).
- **Cheapest insertion ≤ 2**, tight — proved by **domination of nearest insertion** (each cheapest step ≤ a nearest-style step), so the MST appears as an *upper bound*, not an identity. Cheapest is **not** Prim-faithful: it may insert a non-tour-nearest vertex when a cheap gap exists.
- **Farthest insertion:** a *weaker* worst-case factor in the RSL framework yet **empirically best** — the family's worst/average inversion.
- **Random insertion:** strong in practice; the focus of the sharpest modern average-case analysis.

The non-obvious research lesson: cheapest insertion shows the **2-bound is a property of the metric insertion paradigm**, not of any specific selection rule — two different rules (nearest, cheapest) hit the same tight constant by different proofs.

## 3. Average-case theory

- On random Euclidean instances (`OPT ≈ β√n`, BHH 1959), all insertion heuristics are constant-competitive in expectation; precise constants are largely empirical (Johnson–McGeoch).
- Cheapest insertion's empirical ~1.15–1.20 ratio is comparable to nearest, typically a touch better, at a larger per-step constant.
- Rigorous average-case *constants* separating nearest/cheapest/farthest/random remain open — the same gap flagged in the nearest-insertion entry.

## 4. Variants and applications

- **Parallel / savings-style construction:** the "cheapest insertion anywhere" template underlies route-building in **vehicle routing** — Solomon's **I1** insertion (TSPTW) and **Clarke–Wright savings** are cheapest-insertion-flavored; cheapest insertion is the single-route, single-criterion ancestor.
- **Cheapest insertion with regret (regret-k insertion):** instead of the globally cheapest, insert the vertex with the largest *regret* (gap between its cheapest and second-cheapest positions) — a widely used VRP improvement over plain cheapest insertion that avoids "saving the hard cities for last."
- **Convex-hull + cheapest insertion** (Stewart; Golden et al.): seed the sub-tour with the convex hull, then cheapest-insert interior points — a strong Euclidean constructor.
- **Or-insertion / segment insertion:** insert short chains rather than single vertices (kin to Or-opt), used in VRP.

## 5. Open problems / research threads

1. **Tight average-case constants** for cheapest vs nearest vs farthest vs random on random Euclidean (and other) metrics.
2. **Explain the worst/average inversion** rigorously: why farthest (weak worst case) beats cheapest/nearest (tight 2) on typical inputs.
3. **Regret-insertion guarantees:** worst-/average-case analysis of regret-k construction.
4. **Learned selection policies:** RL/GNN-chosen insertion order generalizing nearest/cheapest/farthest; cross-distribution guarantees.
5. **Cheapest-insertion seeds for local search:** smoothed-analysis-style account of why insertion seeds give good 2-opt/LK basins.

## 6. Links to related problems

- **Insertion-family siblings (this registry):** Nearest insertion (≤ 2, Prim-faithful; the proof cheapest dominates), Random / farthest insertion (farthest empirically best — the inversion).
- **Shared 2-bound cousin:** MST doubling (also 2 via the MST). Christofides–Serdyukov (3/2) is the next step up in guarantee.
- **Greedy contrast:** nearest neighbor & greedy-edge (`Θ(log n)`) — insertion's constant bound is the payoff for keeping a feasible sub-tour.
- **Improvement partners:** 2-opt, Or-opt, Lin–Kernighan, chained Lin–Kernighan consume insertion tours as seeds.
- **Paradigm & tooling:** Prim's MST, convex hull, subadditive Euclidean functionals (average-case), Clarke–Wright / Solomon insertion and **regret insertion** for VRP.

## References (seminal → modern)

- Rosenkrantz, Stearns & Lewis (1977). *An analysis of several heuristics for the traveling salesman problem.* (Nearest/cheapest ≤ 2, farthest, etc.)
- Beardwood, Halton & Hammersley (1959). *The shortest path through many points.* (Euclidean `β√n` law.)
- Golden, Bodin, Doyle & Stewart (1980). *Approximate traveling salesman algorithms.* (Convex-hull and insertion comparisons.)
- Johnson & McGeoch (1997). *The TSP: a case study in local optimization.* (Empirical insertion comparisons; farthest best.)
- Solomon, M. (1987). *Algorithms for the vehicle routing and scheduling problems with time window constraints.* (I1 insertion; regret insertion lineage.)
- Clarke, G. & Wright, J. (1964). *Scheduling of vehicles from a central depot…* (Savings construction.)

*Uncertainty flags:* the worst-case constants (nearest/cheapest ≤ 2, tight) are proven; *average-case* constants are empirical and distribution-dependent (Johnson–McGeoch). The "farthest insertion best in practice" claim is empirical. Comparison-table figures are representative, not universal.

