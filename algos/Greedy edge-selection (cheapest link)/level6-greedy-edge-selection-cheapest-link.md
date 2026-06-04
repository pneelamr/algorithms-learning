# Greedy edge-selection (cheapest link) — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Circuit / cable layout  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; depends on instance
> **Used for:** Constructing a tour by adding cheapest valid edges first
> **Level 6 of 6** — researcher: bounds, the matroid-intersection lens, average-case, variants, open problems, references. See sibling files for other levels.

---

## 1. Position among constructive heuristics

Greedy edge-selection (a.k.a. **greedy-edge**, **cheapest-link**, **multi-fragment**) is the edge-centric counterpart of nearest neighbor. Both are `Θ(log n)`-ratio constructive heuristics; the field places them together as the "greedy, no constant guarantee" tier beneath the insertion family:

```
Constructive heuristic        metric worst-case ratio    empirical (rand. Euclidean)
  Nearest neighbor             Θ(log n)                   ~25% over OPT
  Greedy-edge / multi-fragment Θ(log n)                   ~10–15% over OPT   ← this entry
  Nearest / cheapest insertion ≤ 2  (constant!)           ~15–20% over OPT
  Farthest insertion           ~2.43                      ~10–15% over OPT
  Christofides (approx.)        3/2                        ~10% over OPT
Then 2-opt / Or-opt / Lin–Kernighan polish any seed to ~1–5% over OPT.
```

Greedy-edge is often the **best of the simple greedy constructors empirically** (Bentley's "multi-fragment" studies; Johnson–McGeoch), despite sharing NN's poor worst case — a recurring theme that average-case ≠ worst-case for TSP heuristics.

## 2. The matroid-intersection lens (why no constant ratio)

The deep reason greedy-edge lacks MST's optimality: tour-feasible edge sets (**linear forests**) are the common independent sets of **two** matroids — graphic (acyclicity) ∩ partition (degree ≤ 2). The **Rado–Edmonds theorem** makes greedy exact over *one* matroid (Kruskal/MST) but not over a matroid **intersection**. Moreover a single tour is *strictly stronger* than a maximum-weight common independent set of those two matroids (which would be a min-weight 2-factor — a disjoint union of subtours), and enforcing connectivity (one cycle) is the NP-hard step. So greedy-edge is "matroid greedy pushed one matroid too far," and its `Θ(log n)` RSL bound is the quantitative cost of that overreach. This connects TSP construction to **matroid intersection**, **2-factor / 2-matching relaxations**, and the broader theory of when greedy is provably good.

## 3. Worst-case and average-case theory

- **Worst case `Θ(log n)`** (Rosenkrantz–Stearns–Lewis 1977): tight, matching nearest neighbor; the constructive heuristics' fundamental ceiling.
- **Average case:** on random Euclidean instances (`OPT ≈ β√n`, BHH), greedy-edge/multi-fragment empirically yields ~10–15% excess, beating single-start NN; rigorous constant-factor average bounds follow from subadditive Euclidean functional theory (Steele; Yukich), though tight constants are open. The persistent worst/average gap is the practical justification for its use.
- **Greedy matching connection:** greedy-edge is closely related to **greedy weighted matching**, whose own worst/average behavior (e.g. greedy matching is a 1/2-approximation for *max* matching; different for min-weight perfect matching) informs the analysis.

## 4. Variants and applications

- **Multi-fragment heuristic** (Bentley 1992): the same algorithm framed as merging path-fragments; with k-d-tree candidate generation it runs in `O(n log n)` on geometric instances and is a standard fast seed in experimental TSP work.
- **Clarke–Wright savings** (1964): the foundational **VRP** constructor — greedily merge routes by largest "savings" `s_{ij} = d_{0i}+d_{0j}−d_{ij}`; structurally a cheapest-link-style edge-merging greedy with depot constraints. One of the most-used heuristics in logistics.
- **Circuit/cable layout & matching-type problems:** greedy cheapest-compatible-edge selection appears wherever a degree-constrained low-cost subgraph is assembled incrementally.
- **Candidate-list / neighbor-list construction** inside Concorde, LKH: greedy-edge over candidate edges is a common tour initializer.

## 5. Open problems / research threads

1. **Tight average-case constants** for greedy-edge/multi-fragment on random Euclidean (and other random) metrics — closing the gap between the empirical ~1.1–1.15 and rigorous bounds.
2. **Smoothed analysis** of greedy-edge + 2-opt/LK pipelines (why seed+polish is fast and good).
3. **Matroid-intersection-guided constructors:** can a principled (approximate) matroid-intersection / 2-factor relaxation plus connectivity repair beat greedy-edge with a *provable* constant ratio cheaper than Christofides?
4. **Learned edge-scoring constructors:** GNN edge-prediction heatmaps (Joshi et al. 2019) feeding a greedy/beam edge-selection decoder — learned generalizations of cheapest-link; their guarantees and generalization are open.
5. Best **candidate-list size / repair strategy** trade-offs for deadlock-free fast construction.

Greedy-edge itself is theoretically settled (`Θ(log n)` worst case, `Θ(n² log n)` time, matroid-intersection explanation); its live surface is average-case theory, the VRP/savings lineage, and learned edge-selection.

## 6. Links to related problems

- **Constructive siblings (this registry):** Nearest neighbor (vertex-greedy counterpart, same `Θ(log n)`), Nearest/Cheapest/Random–farthest insertion (insertion family, *constant* ratio).
- **Structural cousin:** minimum spanning tree (Kruskal — same greedy, single matroid ⇒ optimal); the contrast is the whole pedagogical point.
- **Improvement partners:** 2-opt, 3-opt, Lin–Kernighan, chained LK consume greedy-edge tours as seeds.
- **Relaxations:** 2-factor / 2-matching, matroid intersection, min-weight perfect matching (Christofides' ingredient); Clarke–Wright savings for VRP.
- **Paradigm:** greedy algorithms & matroid theory (Rado–Edmonds), Union–Find, learned combinatorial construction.

## References (seminal → modern)

- Rosenkrantz, Stearns & Lewis (1977). *An analysis of several heuristics for the traveling salesman problem.* (`Θ(log n)` for greedy-edge and NN.)
- Edmonds, J. (1971). *Matroids and the greedy algorithm*; Rado, R. (1957). (Why greedy is exact over a matroid — and only one.)
- Clarke, G. & Wright, J. (1964). *Scheduling of vehicles from a central depot* (savings heuristic; VRP).
- Bentley, J. (1992). *Fast algorithms for geometric traveling salesman problems.* (Multi-fragment; k-d-tree construction.)
- Beardwood, Halton & Hammersley (1959). *The shortest path through many points.*
- Johnson, D. & McGeoch, L. (1997). *The traveling salesman problem: a case study in local optimization.* (Empirical comparison of constructors and seeds.)
- Joshi, Laurent & Bresson (2019). *An efficient graph convolutional network technique for the TSP* (learned edge heatmaps + greedy/beam decoding).

*Uncertainty flags:* the `Θ(log n)` worst-case ratio is proven; precise average-case constants are empirical with non-tight rigorous bounds. The "multi-fragment beats NN" comparison is empirical and instance-distribution dependent. Learned edge-selection results are distribution-specific without worst-case guarantees. Exact constants in the heuristic-comparison table are representative empirical figures (Johnson–McGeoch-style), not universal.
