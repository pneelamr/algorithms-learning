# Branch-and-bound — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, 0/1 Knapsack, Integer programming, Job scheduling  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour with pruning based on bounds
> **Used for:** General exact search that prunes provably bad subtrees
> **Level 6 of 6** — researcher: SOTA, theoretical frontiers, open problems, references, related problems. See sibling files for other levels.

---

## 1. Position in the exact landscape

Branch-and-bound is the **algorithmic substrate of exact combinatorial optimization** — not a single algorithm but a framework whose power is entirely in its components (bound, branching, search, cuts). For TSP the lineage:

```
Brute force            Θ(n!)            no pruning
Held–Karp DP           O*(2^n)          best worst-case bound, but exponential memory
Branch-and-bound       worst-case Θ(n!), but prunes hugely in practice
   + cutting planes  → Branch-and-cut (Concorde): proven-optimal at 10^4–10^5 cities
   + column gen      → Branch-and-price: VRP, crew scheduling, cutting stock
```

Unlike Held–Karp, B&B offers **no improved worst-case guarantee** — its contribution is *practical* exactness with an **optimality certificate** and an **anytime gap** `(U − L)/U`. It is the engine inside every commercial MILP solver (CPLEX, Gurobi, SCIP) and inside Concorde.

## 2. State of the art

- **Concorde** (Applegate, Bixby, Chvátal, Cook 2006): **branch-and-cut** with a deep separation arsenal — subtour-elimination, **comb**, **clique-tree**, **domino-parity**, and other facet-defining inequalities of the TSP polytope — plus the Held–Karp/Lagrangian bound and local-cut techniques. Solved the 85,900-city `pla85900` instance to proven optimality (2006); routinely closes instances with tens of thousands of cities. The defining demonstration that B&B + polyhedral theory beats the `2ⁿ` DP wall in practice.
- **General MILP:** modern solvers fuse B&B with **cutting-plane generation** (Gomory mixed-integer, MIR, lift-and-project, cover cuts), **presolve**, **primal heuristics** (feasibility pump, RINS, local branching), **conflict analysis** (borrowed from SAT), and sophisticated **branching** (reliability/pseudocost, strong branching, hybrid). Two-to-three orders of magnitude speedups over a decade come mostly from these B&B refinements, not hardware.
- **Branching theory:** *strong branching* gives small trees but is expensive; *pseudocost* and *reliability branching* approximate it cheaply; recent **ML-guided branching** (imitation learning of strong-branching decisions; Khalil et al. 2016; Gasse et al. 2019, GCNN) and **learned node/cut selection** are an active research thread (learning to branch / to cut / to run heuristics).

## 3. Theoretical frontiers

- **Tree-size lower bounds.** How small *can* a B&B tree be? Recent work proves **exponential lower bounds on branch-and-bound (and branch-and-cut) tree sizes** for certain ILPs regardless of branching choices (e.g. Dadush–Tiwari; Dey et al. on B&B for infeasible/hard integer programs), and connects general branching (split disjunctions, lattice-based branching) to the geometry of the integer hull.
- **Cutting-plane proof complexity.** B&C corresponds to **Chvátal–Gomory / split-cut proof systems**; rank and length lower bounds (Chvátal rank, split rank of the TSP/stable-set polytopes) bound how much cuts can help. Ties exact optimization to proof complexity (Cutting Planes vs Resolution).
- **Polyhedral combinatorics of TSP.** The TSP polytope's facet structure is infinitely rich (no complete description; recognizing facets is hard). Which inequality families to separate, and the complexity of separation (the comb/domino-parity separation problem), remain studied.
- **Lagrangian/SDP bounds.** Stronger relaxations (semidefinite, Lasserre/sum-of-squares hierarchies) give tighter bounds at higher cost; where the bound/effort trade-off pays off is open.

## 4. ML for branch-and-bound (the live frontier)

- **Learning to branch:** imitate or improve strong branching with GNNs over the bipartite variable–constraint graph (Gasse et al. 2019); reinforcement learning for branching policies.
- **Learning to cut / to select nodes / to schedule heuristics:** data-driven component selection inside the solver.
- **Theory of learned B&B:** sample-complexity and generalization guarantees for *tuning* branch-and-bound (Balcan et al. — learning provably good branching/tuning parameters from an instance distribution). A bridge between combinatorial optimization and statistical learning theory.

## 5. Open problems

1. **Provably good, cheap branching** — closing the gap between expensive strong branching and fast heuristics with guarantees.
2. **Instance-dependent / smoothed-analysis** explanations for why B&B is fast in practice despite `Θ(n!)` worst case (analogous to simplex smoothed analysis).
3. **Tight tree-size lower bounds** for branch-and-cut on structured problems; the power of general lattice-based branching vs variable branching.
4. **Separation complexity** for strong TSP cut families and automatic discovery of useful cuts (local cuts, learned cuts).
5. **Generalization guarantees** for ML-learned branching/cutting across instance distributions, and robustness to distribution shift.
6. Effective **massively parallel** B&B (deterministic, low-overhead incumbent sharing; taming parallel search anomalies).

## 6. Links to related problems

- **Exact siblings (this registry):** Brute force enumeration (B&B with a trivial bound = full enumeration), Held–Karp DP (whose **1-tree Lagrangian bound** is a classic B&B bounding function — distinct from the HK *DP*), Branch-and-cut / Concorde (B&B + cutting planes — the next entry, the SOTA exact solver).
- **Paradigm:** relaxation (LP/Lagrangian/SDP), cutting-plane methods, polyhedral combinatorics, integer & mixed-integer programming, A* / heuristic search (admissible-bound special case), constraint programming (B&B + propagation), DPLL/CDCL SAT (B&B's Boolean cousin).
- **Decomposition:** branch-and-price (column generation), Benders decomposition, Dantzig–Wolfe.
- **Bounds source:** assignment relaxation (Hungarian), minimum-1-tree, Lagrangian duality, semidefinite relaxations.

## References (seminal → modern)

- Land, A. & Doig, A. (1960). *An automatic method of solving discrete programming problems.* (Branch-and-bound origin.)
- Little, Murty, Sweeney & Karel (1963). *An algorithm for the traveling salesman problem.* (B&B for TSP; matrix-reduction bound.)
- Held, M. & Karp, R. (1970, 1971). *The traveling-salesman problem and minimum spanning trees, I & II.* (1-tree / Lagrangian bound.)
- Balas, E. & Toth, P. (1985). *Branch and bound methods for the TSP.* (Survey.)
- Padberg, M. & Rinaldi, G. (1991). *A branch-and-cut algorithm for the resolution of large-scale symmetric TSPs.* (Branch-and-cut foundations.)
- Applegate, Bixby, Chvátal & Cook (2006). *The Traveling Salesman Problem: A Computational Study* (Concorde).
- Khalil et al. (2016). *Learning to branch in MIP*; Gasse et al. (2019). *Exact combinatorial optimization with GCNNs.*
- Balcan et al. (2018). *Learning to branch.* (Sample-complexity of branching/tuning.)
- Dey, Dubey & Molinaro (2021–); Dadush & Tiwari (2020). *On the complexity / tree-size lower bounds of branch-and-bound.*

*Uncertainty flags:* worst-case `Θ(n!)`-class is unconditional, but the *practical* efficiency of B&B/B&C lacks a fully satisfying theoretical explanation (an open "why does it work so well" akin to simplex). Tree-size lower-bound results are recent and problem-specific; ML-for-B&B guarantees hold under stated distributional assumptions, not worst-case. Concorde's reach depends heavily on instance structure (Euclidean instances are far easier than adversarial ones).
