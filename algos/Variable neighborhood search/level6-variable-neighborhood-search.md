# Variable neighborhood search — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Clustering / p-median  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; systematic neighborhood changes
> **Used for:** Systematically switching neighborhood structures to balance diversify/intensify
> **Level 6 of 6** — researcher: the variant landscape, VND/decomposition, open problems, references. See sibling files for other levels.

---

## 1. Position: near-parameter-free systematic search

Variable neighborhood search is the metaheuristic that turns **"change the neighborhood"** into a first-class design principle. Its appeal is methodological: a handful of natural neighborhoods plus the trivial *reset-on-success/grow-on-failure* rule yields a robust, **nearly parameter-free** solver — no temperature schedule (SA), tenure (tabu), or hand-tuned kick (ILS). It is a leading method on **p-median, clustering, and many VRP variants**, and its **VND** component (descend until optimal under every neighborhood) is a reusable module dropped inside other metaheuristics. On Euclidean TSP it is competitive but not state of the art (Lin–Kernighan / EAX lead).

## 2. The variant landscape

- **VND (Variable Neighborhood Descent):** deterministic multi-neighborhood descent; the core that gives "optimal under all `N_k`" optima — used standalone and as the local search inside GVNS, ILS, GA, etc.
- **RVNS (Reduced VNS):** shake-only, no local search — for very large instances or fast bounds.
- **Basic / General VNS (GVNS):** shake + LS / shake + VND (the strong standard).
- **VNDS (Variable Neighborhood Decomposition Search):** fix all but `k` components and optimize the size-`k` sub-problem exactly/heuristically — a **matheuristic** bridge (sub-problem solved by MIP/exact methods).
- **Skewed VNS:** distance-rewarded acceptance to cross large plateaus.
- **Continuous VNS / VNS for global optimization:** neighborhoods as shrinking balls in `ℝ^n` — VNS beyond combinatorics.

## 3. Theory frontier

- **No guarantees** beyond the VND certificate (optimal w.r.t. the ladder). Convergence/finite-time analysis is open, shared with ILS.
- **Neighborhood-ladder design theory:** which neighborhoods, in what order, provably help — formalizing the empirical art of ladder construction; connections to **landscape correlation** and the big-valley hypothesis.
- **VND ordering:** the order of neighborhoods affects both speed and the optimum reached; principled (or learned) orderings are an active question.
- **Matheuristic VNDS:** integrating exact sub-solvers with VNS decomposition — convergence and decomposition-size theory.

## 4. Relationship and the unifying map

- **ILS ↔ VNS:** both shake-and-reoptimize on `S*`; VNS = systematic, self-scaling, multi-neighborhood diversification, ILS = fixed/adaptive single kick. An ILS with a growing perturbation *is* a VNS; a VNS with one shake neighborhood *is* an ILS.
- **VND ↔ k-opt ladder:** VND generalizes "stack neighborhoods for stronger optima" (3-opt ⊆ 2-opt) to heterogeneous move sets.
- **VNS components reused** as the local search inside tabu search, GA (memetic), and GRASP — VND in particular is a portable intensifier.

## 5. Applications

- **p-median / facility location, clustering, graph problems:** VNS is historically a top performer (Hansen–Mladenović and successors).
- **Vehicle routing (many variants):** GVNS and VNS-based matheuristics are competitive/leading.
- **Continuous & mixed optimization, scheduling, network design, bioinformatics:** broad deployment owing to robustness and minimal tuning.

## 6. Open problems / threads

1. **Ladder/ordering theory:** provable guidance for selecting and ordering neighborhoods (and VND order).
2. **Convergence/finite-time analysis** of (G)VNS on structured landscapes.
3. **Learned neighborhood selection:** RL/bandit choice of which `N_k` to shake/descend, with generalization.
4. **Matheuristic VNDS:** decomposition-size theory and exact-subsolver integration guarantees.
5. **Unification:** formal equivalences among VNS, ILS, and SA-on-`S*`.

## 7. References (seminal → modern)

- Mladenović & Hansen (1997). *Variable neighborhood search.* (The method.)
- Hansen & Mladenović (2001). *Variable neighborhood search: principles and applications.*
- Hansen, Mladenović, Brimberg & Pérez (2019). *Variable Neighborhood Search* (Handbook of Metaheuristics chapter; the modern survey).
- Hansen, Mladenović & Pérez (2010). *Variable neighborhood search: methods and applications* (Annals of OR).
- Brimberg & Mladenović (1996). *VNS for the continuous location-allocation / p-median problems.* (Flagship application.)

## 8. Links to related problems

- **Inner moves / ladder:** 2-opt, Or-opt, 3-opt, swap (TSP); insert/interchange (p-median/VRP) — VND stacks these.
- **Sibling metaheuristics (this registry):** iterated local search (fixed-kick cousin — closest relative), tabu search (memory), simulated annealing (thermal), GRASP (randomized restart), genetic / ant-colony (population). VND is frequently the local search inside GRASP and memetic GAs.
- **Cross-cluster:** VND generalizes the k-opt neighborhood-stacking (local-search cluster); VNDS bridges to exact sub-solvers (branch-and-bound / MIP from the exact cluster) as a matheuristic.

*Uncertainty flags:* VNS has **no guarantee** beyond the VND ladder-certificate. "Top method on p-median/clustering/VRP" and "below LK on Euclidean TSP" are empirical and instance/tuning dependent. The three principles are well-supported design heuristics, and principle 3 (clustered multi-neighborhood optima) is an empirical regularity, not a theorem. Unifying equivalences with ILS are conceptual framings.

