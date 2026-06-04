# Branch-and-cut (ILP / Concorde-style) — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Mixed-integer linear programs, Vehicle Routing Problem, Max-cut  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour; state-of-the-art exact performance
> **Used for:** Research-grade exact solving by combining branching with cutting planes
> **Level 6 of 6** — researcher: SOTA, theoretical frontiers, open problems, references, related problems. See sibling files for other levels.

---

## 1. Position: the exact frontier

Branch-and-cut is the **state-of-the-art exact method** for TSP and for general mixed-integer linear programming — the apex of this registry's exact lineage:

```
Brute force      Θ(n!)             no structure
Held–Karp DP     O*(2^n)           best worst-case bound; exponential memory
Branch-and-bound prune w/ relaxation bounds; worst-case Θ(n!)
Branch-and-cut   B&B + dynamic cutting planes (polyhedral) → Concorde
                 proven-optimal TSP at 10^4–10^5 cities; engine of CPLEX/Gurobi/SCIP
```

It has **no better worst-case guarantee** than enumeration — its supremacy is empirical, resting on polyhedral combinatorics (tight cuts), efficient separation, strong branching, and powerful primal heuristics. The same framework, with general-purpose (GMI/MIR) cuts, is why modern MILP solvers improved ~100–1000× over two decades.

## 2. State of the art

- **Concorde** (Applegate, Bixby, Chvátal, Cook 2006): the reference exact TSP solver. Branch-and-cut with **subtour, comb, clique-tree, bipartition, path, and domino-parity** inequalities; sophisticated separation (safe shrinking, block decomposition, Padberg–Rinaldi heuristics, **local cuts** — instance-specific cuts found by projecting small templates); Lin–Kernighan / chained-LK for primal bounds; exact-arithmetic verification. Solved **pla85900** (85,900 cities, 2006) to proven optimality; the largest nontrivial instances ever certified. Earlier milestones: 2,392 (1987), 7,397 (1994), 13,509 (1998), 24,978 Sweden (2004).
- **Domino-parity cuts** (Letchford 2000; Cook–Espinoza–Goycoolea): polynomial **planar** separation of a comb generalization — decisive for the largest solves.
- **General MILP B&C** (Gurobi, CPLEX, SCIP, COIN-OR): GMI/MIR/cover/flow-cover/clique cuts, presolve, conflict analysis (from SAT/CDCL), reliability/pseudocost & **hybrid branching**, feasibility-pump / RINS / local-branching primal heuristics, and increasingly **ML-guided** components.
- **Vehicle routing:** **branch-and-cut-and-price** (column generation + rounded-capacity/comb cuts) is SOTA exact VRP (Pecin et al.; the BCP solver line).

## 3. Theoretical frontiers

- **Polyhedral combinatorics of `P_TSP`:** no complete facet description (and provably none "nice" unless `NP = coNP`-style collapses); discovering and separating new facet families remains active. The separation complexity of comb/clique-tree inequalities (NP-hard in general; special-case poly algorithms) is studied per family.
- **Separation ⇄ optimization** (Grötschel–Lovász–Schrijver, ellipsoid): poly-time separation ⇔ poly-time optimization over a polytope — the principle that makes "exponentially many constraints, generated on demand" rigorous.
- **Cutting-plane proof complexity:** B&C runs correspond to **Chvátal–Gomory** and **split-cut** proofs of optimality/infeasibility; **Chvátal rank** and **split rank** lower bounds quantify how many cutting rounds are unavoidable. Connections to the **Cutting Planes** proof system vs Resolution (lower bounds via interpolation, e.g. Pudlák).
- **Tree-size / cut lower bounds:** recent results prove **exponential lower bounds on branch-and-bound and branch-and-cut tree sizes** for families of integer programs irrespective of branching/cut choices (Dadush–Tiwari; Dey et al.; Basu et al. on the power of split/lattice cuts) — formalizing intrinsic limits.
- **Stronger relaxations:** **SDP / Lasserre–sum-of-squares** hierarchies give tighter bounds than LP cuts at higher cost; the bound-strength/effort trade-off and lift-and-project hierarchies (Sherali–Adams, Lovász–Schrijver) are theoretically central.

## 4. ML and automation (live frontier)

- **Learning to cut:** RL/imitation policies for *which* cuts to add and *when* (Tang et al. 2020; Paulus et al. 2022) — cut selection strongly affects performance and is poorly understood theoretically.
- **Learning to branch:** GNN imitation of strong branching (Gasse et al. 2019), with sample-complexity theory for tuning (Balcan et al.).
- **Configuration / algorithm selection:** data-driven solver tuning across instance distributions; the theory of *learning provably good* B&C parameters.

## 5. Open problems

1. **Smoothed/instance-dependent analysis** explaining B&C's practical speed despite worst-case exponentiality (a TSP/MILP analogue of simplex smoothed analysis).
2. New **facet families and efficient (exact) separation** for `P_TSP` and structured MILPs; automated cut discovery (local cuts, learned cuts) with guarantees.
3. **Tight tree-size and cut-rank lower bounds** for branch-and-cut on natural problems; the relative power of general lattice/split branching vs variable branching.
4. **Stronger-but-tractable relaxations** (SDP hierarchies) that pay off in end-to-end exact solving.
5. **Generalization & robustness** of ML-learned cut/branch policies across distributions and to adversarial instances.
6. **Certified / exact-arithmetic** B&C at scale (verified optimality without floating-point trust).

## 6. Links to related problems

- **Exact siblings (this registry):** Branch-and-bound (B&C is B&B + dynamic cuts; same correctness skeleton), Held–Karp DP (its subtour/1-tree bound **is** B&C's starting LP relaxation value), Brute force (the degenerate no-bound baseline).
- **Primal-bound partners:** Lin–Kernighan / chained Lin–Kernighan supply the heuristic incumbents that make B&C prune hard (improvement-heuristic siblings).
- **Paradigm:** integer & mixed-integer programming, cutting-plane methods (Gomory), polyhedral combinatorics, LP duality, column generation (branch-and-price), Benders decomposition, semidefinite relaxations & SOS hierarchies, constraint programming hybrids.
- **Separation tooling:** max-flow / min-cut, Gomory–Hu trees, planar separation (domino-parity).

## References (seminal → modern)

- Dantzig, Fulkerson & Johnson (1954). *Solution of a large-scale traveling-salesman problem.* (Cutting planes + the DFJ/SEC formulation — the origin.)
- Gomory, R. (1958/1963). *Outline of an algorithm for integer solutions to linear programs* (general cutting planes; GMI).
- Grötschel, Lovász & Schrijver (1981/1988). *The ellipsoid method and combinatorial optimization* (separation ⇔ optimization).
- Grötschel, M. & Padberg, M. (1979). *On the symmetric travelling salesman problem* I/II (comb & facet theory).
- Padberg, M. & Rinaldi, G. (1991). *A branch-and-cut algorithm for large-scale symmetric TSPs.*
- Letchford, A. (2000). *Separating a superclass of comb inequalities in planar graphs* (domino-parity).
- Applegate, Bixby, Chvátal & Cook (2006). *The Traveling Salesman Problem: A Computational Study* (Concorde).
- Gasse et al. (2019). *Exact combinatorial optimization with GCNNs*; Tang, Agrawal & Faenza (2020). *Reinforcement learning for integer programming: learning to cut.*
- Balcan et al. (2018). *Learning to branch*; Dey, Dubey & Molinaro (2021–) and Dadush & Tiwari (2020) on B&B/B&C tree-size lower bounds.

*Uncertainty flags:* worst-case exponentiality is unconditional, but a rigorous explanation of B&C's *practical* efficiency is open. Largest-solved-instance figures are as of the knowledge cutoff and are highly instance-structure-dependent (Euclidean ≪ adversarial). Separation complexity varies by cut family (SEC poly via min-cut; general comb separation NP-hard). ML-for-B&C guarantees hold under distributional assumptions, not worst case.
