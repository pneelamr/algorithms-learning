# Held–Karp dynamic programming — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Shortest Hamiltonian path, Sequential ordering / scheduling with setup costs  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour; time O(n^2·2^n)
> **Used for:** Exact solving and benchmarking for small–medium instances via subset DP
> **Level 6 of 6** — researcher: the O*(2ⁿ) frontier, space–time trade-offs, lower bounds, open problems, references. See sibling files for other levels.

---

## 1. Position: the 60-year-old bound nobody has beaten

Held–Karp (Held & Karp 1962; Bellman 1962) gives `O(n²2ⁿ)` time, `O(n2ⁿ)` space — the textbook subset DP. Its singular status: **`O*(2ⁿ)` is still the best known worst-case running time for general (asymmetric, arbitrary-weight) TSP.** No `O*(2^{(1−ε)n})` algorithm is known for any `ε > 0`. So unlike most 1962 algorithms, Held–Karp sits *on* the frontier, not behind it — the open question is whether its base of 2 can be lowered at all.

```
Brute force        2^{Θ(n log n)}      no structure
Held–Karp DP       O*(2^n) time, O*(2^n) space      ← best known TIME bound, general TSP
Inclusion–excl.    O*(2^n) time, poly space         ← dominates HK on SPACE
Branch-and-cut     exp worst case, but solves 10^4–10^5 cities in practice (Concorde)
```

## 2. Space is the real enemy — the time/space frontier

The `Θ(2ⁿ)` **memory** binds long before the time does. Lines of attack:

- **Inclusion–exclusion (poly-space `O*(2ⁿ)`):** count/optimize Hamiltonian paths via an I–E formula over subsets (Kohn–Gottlieb–Kohn 1977; Karp 1982; Bax 1993). Achieves `O*(2ⁿ)` *time in polynomial space* — strictly dominating Held–Karp, which is why HK's continued use is about *simplicity and constant factors*, not Pareto-optimality.
- **Held–Karp with measure-and-conquer / divide-and-conquer over the subset lattice:** time–space trade-off curves `O*(2ⁿ/ S)`-ish for space `S`; Björklund's work and the broader *exact exponential algorithms* program (Fomin & Kratsch 2010) map this surface.
- **Björklund's `O*(1.657ⁿ)` for undirected Hamiltonicity** (FOCS 2010): a randomized, algebraic (Tutte-matrix / determinant-over-GF(2^k)) algorithm that breaks `2ⁿ` — but for the **decision** problem (does a Hamilton cycle exist), and **undirected** graphs. Extending the sub-`2ⁿ` base to **optimization** TSP and to **directed/weighted** instances is open; for **bounded-degree** and **bipartite** graphs further improvements exist (Björklund–Husfeldt; Cygan–Pilipczuk).
- **Polynomial-space branching / Monte-Carlo** for Hamiltonicity (Nederlof; Björklund–Husfeldt) trade randomization for space.

## 3. Conditional lower bounds — is `2ⁿ` tight?

- **SETH-based barriers:** improving the base for several `2ⁿ`-DP problems (Set Cover, Steiner Tree, Hamiltonicity) is conjectured hard; Cygan et al. (2016), *"On problems as hard as CNF-SAT,"* gives reductions suggesting `O*((2−ε)ⁿ)` for some of these would refute SETH. For **directed** Hamiltonicity / TSP specifically, no `O*((2−ε)ⁿ)` is known and SETH-style evidence hints at hardness — but a clean unconditional lower bound is **open**.
- **NP-hardness** (Karp 1972) rules out poly-time exact (under `P≠NP`) but says nothing about the exponential base; fine-grained complexity is the right lens.
- The gap between the **undirected** sub-`2ⁿ` Hamiltonicity result and the **directed/weighted optimization** wall is one of the cleanest open separations in exact algorithms.

## 4. Held–Karp the DP vs Held–Karp the bound (do not conflate)

Same authors, two famous objects:
- **This artifact:** the 1962 **subset DP** (exact `O*(2ⁿ)`).
- **The Held–Karp lower bound** (1970/71): the **1-tree / Lagrangian relaxation** bound `HK = max_π [ minimum-1-tree cost under node potentials π ]`, the LP relaxation value of the subtour-elimination formulation. Empirically within ~0.8–2% of the optimum on Euclidean instances, it is the *workhorse bounding function inside branch-and-bound* and a key ingredient of Concorde's success. The DP solves; the bound *prunes*. They meet inside exact solvers but are different contributions.

## 5. Practical role today

- **Exact oracle for `n ≲ 20`:** validating heuristics (LK, Christofides, neural solvers), generating optimal labels for learning-to-route ML, exact sub-solves inside decomposition (clustering, column generation, large-neighborhood search exact repair).
- **Sequencing with side constraints:** the subset-DP template generalizes cleanly to **TSP with time windows / precedence (SOP)**, **prize-collecting / orienteering**, and **scheduling with sequence-dependent setup times** by enriching the state (add time/last-job/resource); the `2ⁿ` set is the reusable core. This is why HK matters beyond TSP — it is the canonical exponential DP pattern.
- **Bitmask DP** is the competitive-programming and OR-teaching archetype; cache-aware mask ordering and popcount-layered memory are standard engineering refinements.

## 6. Open problems

1. **Sub-`2ⁿ` for weighted/directed TSP optimization** (not just undirected Hamiltonicity decision) — the headline open question.
2. **Unconditional** (or tight SETH-conditional) lower bound for exact TSP's exponential base.
3. Improved **polynomial-space** exact time, or sharp time–space trade-off lower bounds.
4. Derandomizing Björklund-style algebraic Hamiltonicity algorithms.
5. Parameterized exact algorithms (treewidth, bounded genus, degree) narrowing the gap to general instances.

## 7. Links to related problems

- **Exact siblings (this registry):** Brute force enumeration (the `Θ(n!)` baseline HK exponentially beats), Branch-and-bound (uses the HK 1-tree *bound* to prune), Branch-and-cut / Concorde (the practical exact state of the art).
- **Paradigm:** dynamic programming over subsets / bitmask DP, exact exponential algorithms, inclusion–exclusion, algebraic (determinant/permanent) methods for Hamiltonicity.
- **Generalizations:** TSP with time windows, sequential ordering problem (SOP), prize-collecting TSP, scheduling with setup costs — all instances of "subset-DP + enriched state."
- **Complexity:** NP-completeness (Karp 1972), ETH/SETH, fine-grained complexity of `2ⁿ`-DP problems.

## References (seminal → modern)

- Held, M. & Karp, R. (1962). *A dynamic programming approach to sequencing problems.* SIAM J. (the DP in this artifact).
- Bellman, R. (1962). *Dynamic programming treatment of the travelling salesman problem.* JACM (independent, same DP).
- Karp, R. (1972). *Reducibility among combinatorial problems.* (TSP NP-completeness.)
- Held, M. & Karp, R. (1970, 1971). *The traveling-salesman problem and minimum spanning trees, I & II.* (The 1-tree / Lagrangian **bound** — distinct from the DP.)
- Kohn, Gottlieb & Kohn (1977); Karp, R. (1982). *Dynamic programming meets the principle of inclusion–exclusion.* (Polynomial-space `O*(2ⁿ)`.)
- Bax, E. (1993). *Inclusion and exclusion algorithm for the Hamiltonian path problem.*
- Björklund, A. (2010). *Determinant sums for undirected Hamiltonicity.* FOCS (`O*(1.657ⁿ)`).
- Cygan et al. (2016). *On problems as hard as CNF-SAT.* (SETH barriers for `2ⁿ`-DP problems.)
- Fomin, F. & Kratsch, D. (2010). *Exact Exponential Algorithms* (the `O*(2ⁿ)` landscape and trade-offs).
- Applegate, Bixby, Chvátal & Cook (2006). *The Traveling Salesman Problem: A Computational Study* (Concorde; HK bound in practice).

*Uncertainty flags:* whether the `2ⁿ` time base can be beaten for general weighted/directed TSP is **open** as of the knowledge cutoff; Björklund's sub-`2ⁿ` result is specifically randomized, undirected, and for the Hamiltonicity *decision* problem, and does not transfer to weighted optimization. Lower-bound claims are SETH/ETH-conditional, not unconditional.
