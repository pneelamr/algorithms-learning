# 2-opt local search — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; big practical improvement
> **Used for:** Improving a tour by uncrossing pairs of edges until no swap helps
> **Level 5 of 6** — graduate: local-search formalism, the uncrossing theorem, PLS-completeness, smoothed analysis, data structures. See sibling files for other levels.

---

## 1. Local search, formally

A local-search problem is a triple `(F, c, N)`: feasible solutions `F` (here Hamiltonian cycles), cost `c` (tour length), and a **neighborhood** `N: F → 2^F`. 2-opt fixes `N₂(T)` = all tours obtained from `T` by one 2-exchange. The algorithm is **iterative improvement**: while `∃ T' ∈ N₂(T)` with `c(T') < c(T)`, move to `T'` (some `T'`, under a *pivoting rule* — first-improvement or best-improvement). Termination yields a **local optimum**: `c(T) ≤ c(T')` for all `T' ∈ N₂(T)`.

The **2-exchange** deletes edges `{t1,t2},{t3,t4}` and adds `{t1,t3},{t2,t4}`, reversing the intervening path. Crucially, of the three perfect matchings on four endpoints, one is the original tour, one disconnects the cycle into two subtours (infeasible), and exactly one is the valid alternative — so `|N₂(T)| = Θ(n²)` and each neighbor is uniquely determined by the deleted pair.

## 2. The uncrossing theorem (Euclidean)

**Proposition.** In the Euclidean plane, if a tour has two edges `{a,b},{c,d}` that **cross** geometrically, the 2-exchange replacing them with `{a,c},{b,d}` (suitably oriented) strictly shortens the tour.

*Proof.* Let the segments `ab` and `cd` intersect at `p`. By the triangle inequality, `|ac| ≤ |ap|+|pc|` and `|bd| ≤ |bp|+|pd|`, with at least one strict because the four points are not collinear. Summing, `|ac|+|bd| < (|ap|+|pb|)+(|cp|+|pd|) = |ab|+|cd|`. The crossing-free reconnection is the valid one, so the move is improving. ∎

**Corollary.** Every 2-optimal Euclidean tour is **non-self-crossing** (planar). This is the geometric meaning of 2-optimality and explains why 2-opt is so effective on geometric data: it eliminates the single most visible class of defects a constructor leaves behind.

## 3. Quality bounds

For metric/Euclidean TSP, let `2OPT(I)` denote the worst 2-optimal tour on instance `I`:
- **Worst case (Chandra–Karp–Tovey 1999):** there are Euclidean instances with `2OPT/OPT = Θ(log n / log log n)`. Plain metric instances admit ratio `Θ(√n)` constructions for general (non-Euclidean) metrics in some analyses; the clean, citable Euclidean bound is the `Θ(log n / log log n)` one.
- **Average case:** on `n` uniform points in the unit square, the *expected* 2-opt local optimum is within a small constant — empirically ≈ **5%** — of `OPT ≈ β√n` (the BHH constant from the constructive-heuristics analyses). So worst case and typical case diverge sharply, motivating *smoothed* analysis below.

There is **no constant-factor worst-case guarantee**, in contrast to the constructive 2-approximations (MST doubling, nearest insertion). 2-opt buys typical-case excellence at the price of provable safety.

## 4. Complexity of finding a local optimum

Two distinct complexity questions:

**(a) How many improving steps?** Worst case is **exponential**. There exist instances (and even Euclidean instances, Englert–Röglin–Vöcking) on which 2-opt with an adversarial pivoting rule takes `2^{Ω(n)}` improving moves to converge.

**(b) How hard is the local-optimum *problem*?** Computing *any* 2-opt local optimum is **PLS-complete** (Polynomial Local Search; Krentel, Johnson–Papadimitriou–Yannakakis framework; the TSP/k-opt result via standard reductions). PLS-completeness means a polynomial-time algorithm to find a 2-opt local optimum (even without following the improvement path) would collapse `PLS ⊆ P` and imply, e.g., poly-time local optima for all PLS problems — believed false. It also implies the *standard local-search algorithm* has exponential worst-case running time under tight reductions.

**(c) Smoothed analysis (Englert–Röglin–Vöcking 2007/2014).** Perturb each point's coordinates by Gaussian noise of std-dev `σ`. The *expected* number of 2-opt steps is polynomial in `n` and `1/σ`. This is the rigorous explanation of the empirical gap between (a)'s exponential lower bound and 2-opt's reliably fast real-world convergence: pathological instances are measure-zero-fragile.

## 5. Data structures: the reversal bottleneck

The gain is `O(1)`, but **applying** a move reverses a segment. Representation matters:
- **Array / doubly-linked list:** reversal is `O(n)`; also you must flip orientation, so a plain DLL needs a "between" query.
- **Two-level doubly-linked list (Or et al.; Fredman–Johnson–McGeoch–Ostheimer 1995):** `next`, `prev`, `between`, and `reverse` all in `O(√n)` amortized — the standard choice up to ~10⁶ cities.
- **Splay trees / two-level trees:** `O(log n)` per operation, better for very large `n`, with lazy reversal bits.

The practical 2-opt loop = neighbor lists (candidate `t3`) + don't-look bits (skip stale cities) + a `√n`-reversal structure, giving near-linear empirical passes.

## 6. Edge cases & invariants

- **Symmetry required.** A 2-exchange reverses a segment; on **asymmetric** TSP that reverses every intra-segment arc, so the gain is *not* a 4-term local quantity and 2-opt loses its efficiency (use Or-opt / 3-opt variants that avoid reversal, or asymmetric-specific moves).
- **Invariant.** `T` is a Hamiltonian cycle at all times; the infeasible (subtour-splitting) reconnection is never taken.
- **Pivoting rule** affects both the local optimum reached and the step count; first-improvement with neighbor-list ordering is the practical default.
- **ε-tolerance** guards against float cycling.

## 7. Synthesis

2-opt is iterative improvement over the `Θ(n²)` 2-exchange neighborhood. Its local optima are exactly the **crossing-free** tours; it reaches them fast in practice (smoothed-polynomial) despite exponential worst cases and PLS-completeness, lands ~5% above optimal on geometric data, and carries **no** worst-case ratio. It is the minimal, foundational move whose two generalizations — fixing a *larger* `k` (**3-opt**) and letting `k` *vary adaptively* (**Lin–Kernighan**) — define the rest of this cluster. The recurring lesson versus the constructive tier: improvement heuristics swap provable constant ratios for empirically superior tours.

