# 3-opt local search — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; better than 2-opt, slower
> **Used for:** Improving a tour via 3-edge reconnections for higher quality than 2-opt
> **Level 6 of 6** — researcher: the k-opt ladder frontier, Or-opt, complexity, open problems, references. See sibling files for other levels.

---

## 1. Position: the last fixed-k rung

3-opt is the practical ceiling of **fixed-`k`** local search. Beyond it, two design pressures take over: (i) the `Θ(n^k)` neighborhood makes `k ≥ 4` uneconomical for marginal gains, and (ii) the most useful capability 3-opt adds — **segment relocation** — is captured almost entirely by the `Θ(n²)` **Or-opt** restriction. Consequently, modern practice rarely runs full 3-opt: it runs **2-opt + Or-opt** (most of 3-opt's quality, near-2-opt cost) or jumps to **variable-depth Lin–Kernighan**, which dominates fixed 3-opt on the quality/time frontier. 3-opt's enduring role is conceptual: it is where the *diminishing-returns* curve of the k-opt ladder becomes visible and where the **double-bridge gap** that motivates restart methods is first identified.

## 2. Theory frontier

- **PLS-completeness.** Finding a 3-opt (k-opt) local optimum is PLS-complete; standard-improvement convergence is exponential in the worst case under tight reductions (Krentel 1989; Johnson–Papadimitriou–Yannakakis 1988; Schäffer–Yannakakis 1991).
- **Neighborhood monotonicity.** `k`-optimal ⊆ `(k−1)`-optimal is classical; the precise *quality* separations between 2-opt and 3-opt local optima (worst- and average-case constants) remain only partly characterized.
- **Smoothed analysis.** The Englert–Röglin–Vöcking smoothed-polynomial framework is developed for 2-opt; tight smoothed bounds for 3-opt / Or-opt are comparatively open.
- **Counting & geometry.** The structure of the 3-opt improvement graph and the count of 3-optimal tours connect to TSP-polytope facet structure (branch-and-cut entry) and to the planarity of 2-optimal Euclidean tours.

## 3. Variants and engineering

- **Or-opt** (Or 1976): relocate length-1–3 chains; `Θ(n²)`; the standard stand-in for pure 3-opt and the ATSP-safe move.
- **Or-2opt / 2.5-opt hybrids:** interleave 2-opt and short relocations — the common production polish.
- **Neighbor lists + don't-look bits + two-level/`√n` tour structures** (Bentley 1992; Fredman–Johnson–McGeoch–Ostheimer 1995) carry over directly and are essential to make any 3-opt-class scan tractable.
- **Sequential generalization:** Lin–Kernighan replaces "fixed 3 edges, 7 reconnections" with "build a sequential chain of swaps, depth chosen by the gain criterion," recovering `k`-opt-quality moves adaptively. LKH (Helsgaun) extends this to sequential 5-opt moves with `α`-nearness candidate lists from the **Held–Karp 1-tree** bound — tying 3-opt's lineage back to the exact-methods cluster.

## 4. Open problems / threads

1. **Quality separation constants:** rigorous worst-/average-case ratio of 3-opt vs 2-opt local optima on uniform Euclidean inputs.
2. **Smoothed complexity of 3-opt / Or-opt:** polynomial smoothed bounds analogous to 2-opt.
3. **Optimal fixed-`k` vs variable-depth crossover:** a principled account of *why and when* LK's variable depth dominates fixed 3-opt.
4. **Learned reconnection selection:** neural policies choosing among the 7 (or Or-opt) reconnections; guarantees and cross-distribution generalization.
5. **ATSP local search:** strong relocation-based neighborhoods that avoid reversal, with analysis.

## 5. References (seminal → modern)

- Lin, S. (1965). *Computer solutions of the traveling salesman problem.* (Introduces 3-opt and the k-opt notion.)
- Or, I. (1976). *Traveling-salesman-type combinatorial problems…* (Or-opt segment relocation.)
- Lin, S. & Kernighan, B. (1973). *An effective heuristic algorithm for the traveling-salesman problem.* (Variable-depth successor.)
- Bentley, J. (1992). *Fast algorithms for geometric TSP.* (Engineering: neighbor lists, don't-look bits.)
- Fredman, Johnson, McGeoch & Ostheimer (1995). *Data structures for traveling salesmen.*
- Johnson & McGeoch (1997). *The TSP: a case study in local optimization.* (2-opt/3-opt/Or-opt empirical comparison.)
- Schäffer & Yannakakis (1991). *Simple local search problems that are hard to solve.* (PLS-completeness.)

## 6. Links to related problems

- **Down the ladder:** 2-opt (the base move, 3 of the 7 reconnections).
- **Up / sideways:** Lin–Kernighan (variable depth, dominant), chained Lin–Kernighan (double-bridge kicks — the 4-opt move 3-opt cannot make).
- **Practical restriction:** Or-opt (the `Θ(n²)` relocation used in place of full 3-opt).
- **Seed producers:** nearest neighbor, greedy-edge, nearest insertion, Christofides.
- **Cross-cluster:** Held–Karp 1-tree → α-nearness candidate lists in LKH (exact-methods cluster); double-bridge → iterated local search / metaheuristics cluster (SA, tabu, GRASP, VNS).

*Uncertainty flags:* the ~3% average-excess and the "2-opt+Or-opt ≈ 3-opt quality" claims are empirical (Johnson–McGeoch) and distribution-dependent. Neighborhood-monotonicity (`3-opt ⊆ 2-opt`) is exact; quality-separation constants are largely open. PLS-completeness is for the standard k-opt formulation.

