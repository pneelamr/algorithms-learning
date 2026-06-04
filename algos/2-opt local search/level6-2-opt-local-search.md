# 2-opt local search — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; big practical improvement
> **Used for:** Improving a tour by uncrossing pairs of edges until no swap helps
> **Level 6 of 6** — researcher: theory frontier, data structures at scale, smoothed analysis, open problems, references. See sibling files for other levels.

---

## 1. Position: the canonical local search

2-opt is the **base case of the k-opt ladder** and the most-studied local-search operator in combinatorial optimization. It anchors the improvement tier the way brute force anchors the exact tier: rarely the final answer, but the lens through which everything above it (3-opt, Or-opt, Lin–Kernighan, chained LK) is understood. Its practical relevance today is as (i) a fast polish on constructed tours, (ii) the inner move inside richer searches, and (iii) the canonical testbed for local-search *theory* (PLS, smoothed analysis, approximation of local optima).

## 2. Theory frontier

- **PLS-completeness.** Finding a 2-opt (and k-opt) local optimum is PLS-complete; consequently the standard-improvement algorithm has exponential worst-case running time under tight PLS reductions (Krentel; Johnson–Papadimitriou–Yannakakis; Schäffer–Yannakakis for tightness).
- **Smoothed complexity.** Englert, Röglin & Vöcking (*Algorithmica* 2014; FOCS/SODA precursors) prove the expected number of 2-opt steps is `poly(n, 1/σ)` under Gaussian perturbation of `d`-dimensional point sets — the definitive account of why 2-opt converges quickly in reality despite `2^{Ω(n)}` worst-case lower bounds (which they also exhibit, even in the Euclidean plane).
- **Approximation quality of local optima.** Chandra, Karp & Tovey (*SIAM J. Comput.* 1999): worst 2-opt optimum is `Θ(log n / log log n)·OPT` for Euclidean instances; constant-factor *average* ratios for random inputs. The gap between "no constant worst-case bound" and "≈5% in practice" remains a model case for average-case algorithmics.
- **Combinatorial structure.** 2-opt local optima are crossing-free (planar) for Euclidean inputs; the *number* of distinct 2-optimal tours and the structure of the improvement-DAG connect to the geometry of the TSP polytope studied in the **branch-and-cut** entry.

## 3. Data structures at scale

Reversal — not gain — is the bottleneck. The lineage:
- **Two-level doubly-linked list** (Fredman, Johnson, McGeoch & Ostheimer, *J. Algorithms* 1995): `O(√n)` per `reverse/between`, the workhorse for `n` up to ~10⁶.
- **Splay-tree / two-level tree tour representations**: `O(log n)` operations with lazy reversal bits, used in the largest instances and inside LKH.
- Combined with **neighbor (candidate) lists**, **don't-look bits**, and **segment-tree gain caches**, well-engineered 2-opt processes million-city instances in minutes — see Bentley (*ORSA J. Computing* 1992) and Johnson & McGeoch's case study.

## 4. Variants and relatives

- **2h-opt / 2.5-opt:** 2-opt augmented with single-node relocation — a cheap quality bump.
- **Or-opt:** relocate short chains (length 1–3) without reversal; a restricted 3-opt move that is the asymmetric-TSP-friendly cousin (no segment reversal needed).
- **k-opt (general):** the ladder this entry begins; sequential k-opt is generalized by Lin–Kernighan's *variable* depth.
- **Parallel/GPU 2-opt:** neighborhood evaluation parallelizes naturally; used as a building block in large-scale and learned solvers.
- **Learned local search:** neural/RL policies that *propose* 2-opt moves (e.g. learning-to-improve models, 2021–) — the move set is fixed (2-exchange), the *pivoting policy* is learned.

## 5. Open problems / threads

1. **Tight smoothed bounds** and dependence on dimension/metric; closing constants in Englert–Röglin–Vöcking.
2. **Average-case approximation ratio** of 2-opt local optima for uniform Euclidean inputs — rigorous constants (empirically ≈1.05) are still open.
3. **Quality of best-improvement vs first-improvement** pivoting: provable separations in step count and solution quality.
4. **Learned pivoting with guarantees:** when does a learned move-selector provably beat neighbor-list ordering, and does it generalize across instance distributions?
5. **PLS-vs-quality:** structural characterization of which instances force exponential 2-opt convergence.

## 6. References (seminal → modern)

- Croes, G. (1958). *A method for solving traveling-salesman problems.* (Origin of the 2-opt move.)
- Lin, S. (1965). *Computer solutions of the traveling salesman problem.* (2-opt/3-opt as systematic local search.)
- Bentley, J. (1992). *Fast algorithms for geometric traveling salesman problems.* (Neighbor lists, don't-look bits, engineering.)
- Fredman, Johnson, McGeoch & Ostheimer (1995). *Data structures for traveling salesmen.* (Two-level lists; `O(√n)` reversal.)
- Chandra, Karp & Tovey (1999). *New results on the old k-opt algorithm for the TSP.* (Worst/average ratios.)
- Johnson & McGeoch (1997). *The TSP: a case study in local optimization.* (Definitive empirical study.)
- Englert, Röglin & Vöcking (2014). *Worst case and probabilistic analysis of the 2-opt algorithm for the TSP.* (Smoothed analysis.)
- Schäffer & Yannakakis (1991). *Simple local search problems that are hard to solve.* (PLS-completeness machinery.)

## 7. Links to related problems

- **Up the k-opt ladder:** 3-opt (richer reconnections), Lin–Kernighan (variable depth), chained Lin–Kernighan (escaping 2-/3-opt local optima via double-bridge kicks).
- **Seed producers (this registry):** nearest neighbor, greedy-edge, nearest insertion, Christofides — 2-opt polishes any of them.
- **Theory neighbors:** the TSP polytope and crossing-free structure (branch-and-cut entry); BHH `β√n` law and the MST lower bound (constructive-heuristic entries).
- **Forward to metaheuristics:** simulated annealing, tabu search, GRASP, iterated local search — all use a local-search move (often 2-opt) as their inner engine; chained LK is the TSP-specialized instance of that idea.

*Uncertainty flags:* the ≈5% average-gap and ≈1.05 ratio figures are empirical (Johnson–McGeoch) and distribution-dependent. The `Θ(log n / log log n)` worst case is for Euclidean instances specifically; general-metric constructions differ. Smoothed-polynomial bounds hold under the stated Gaussian-perturbation model.

