# Iterated local search — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; layered on local search
> **Used for:** Repeatedly perturbing then re-optimizing a local optimum
> **Level 6 of 6** — researcher: the unifying framework, big-valley theory, adaptivity, open problems, references. See sibling files for other levels.

---

## 1. Position: the minimal high-performance metaheuristic

Iterated local search is the **simplest framework that reaches state-of-the-art quality**, and the conceptual backbone of the single-solution metaheuristics. Its importance is twofold: (i) as the *abstraction* of chained Lin–Kernighan and the lens that unifies SA/TS/VNS/GRASP as "escape operators on the local-optima landscape," and (ii) as a *practical default* — on many problems, ILS with a decent local search and a sensible perturbation matches or beats far more elaborate methods, giving the best performance-to-complexity ratio in the field (Lourenço, Martin & Stützle). On TSP its LK instantiation (chained LK) is a top method; across scheduling/QAP it is a robust baseline.

## 2. Why it works: big-valley / landscape theory

ILS exploits the **big-valley** structure of `S*` for many problems: local optima are not uniformly scattered but cluster, with better optima closer (in solution distance) to each other and to the global optimum. A correlated perturbation (double bridge) therefore walks *down the valley*. Open theory questions:
- **Rigorous big-valley characterization** for Euclidean TSP and beyond (partial, empirical).
- **Optimal perturbation strength** as a function of landscape correlation length and instance size.
- **Mixing/hitting times** of the `S*` walk under various acceptance criteria — finite-time analyses are scarce; the **large-step Markov chain** (Martin–Otto–Felten) gives the SA-on-`S*` framing but not tight bounds.

## 3. Adaptivity and the frontier

- **Adaptive perturbation:** strength schedules reacting to stagnation (toward VNS's neighborhood ladder); learned perturbation operators (RL-chosen kicks, 2020s).
- **Acceptance learning:** tuned/annealed acceptance; bandit-style selection among perturbations.
- **Population ILS / ILS + path relinking:** maintain an elite pool and recombine local optima — ILS reaching toward memetic/scatter-search territory.
- **Restart vs. perturbation portfolios:** principled interpolation between ILS (perturb incumbent) and GRASP (independent restart).
- **Parallel ILS:** independent or cooperating walks on `S*` for very large instances.

## 4. Relationship to the rest of the registry (the unifying map)

ILS is where the cluster's structure becomes explicit:
- **Chained Lin–Kernighan = ILS(LK, double-bridge)** — the exact specialization (local-search cluster).
- **Simulated annealing** = ILS-Metropolis collapsed to single-move steps; equivalently ILS-Metropolis = SA on `S*`.
- **Variable neighborhood search** = ILS whose perturbation is a **systematic neighborhood change** (a growing `N_k` ladder) rather than a fixed kick.
- **GRASP** = ILS with the incumbent-reuse removed (randomized-greedy **restart** each iteration).
- **Tabu search** = the deterministic-memory alternative escape.
- **Memetic GA** = the *population* analogue (recombination instead of perturbation over `S*`).

## 5. Open problems / threads

1. **Finite-time guarantees** for ILS on structured landscapes; provable advantage over random restart.
2. **Perturbation theory:** optimal, instance-adaptive kick strength tied to landscape correlation.
3. **Learned perturbation/acceptance** with cross-instance generalization.
4. **Unification theory:** formal equivalences/separations among ILS, VNS, GRASP, and SA-on-`S*`.
5. **Population ILS:** when recombination of local optima provably beats single-incumbent perturbation.

## 6. References (seminal → modern)

- Lourenço, Martin & Stützle (2003; rev. 2019). *Iterated Local Search* (and *...: Framework and Applications*). (The defining treatment.)
- Martin, Otto & Felten (1991). *Large-step Markov chains for the TSP.* (ILS-Metropolis; the LSMC view; double bridge.)
- Applegate, Cook & Rohe (2003). *Chained Lin–Kernighan for large TSPs.* (The flagship ILS instantiation.)
- Johnson & McGeoch (1997). *The TSP: a case study in local optimization.* (ILS vs. other local-search methods, empirically.)
- Stützle, T. (1998/2006). *Iterated local search for the QAP* and analyses. 
- Boese, Kahng & Muddu (1994). *A new adaptive multi-start technique...* (big-valley evidence for TSP).

## 7. Links to related problems

- **Inner local search:** 2-opt, Or-opt, 3-opt, Lin–Kernighan — ILS searches over their local optima; warm-restart data structures (don't-look bits, `O(√n)` tour list) shared with that cluster.
- **Exact specialization:** chained Lin–Kernighan / LKH; supplies upper bounds to Concorde branch-and-cut.
- **Sibling metaheuristics (this registry):** simulated annealing (= SA on `S*` via Metropolis acceptance), tabu search (memory escape), variable neighborhood search (neighborhood-change escape), GRASP (restart escape), genetic / ant-colony (population).
- **Operators:** double bridge (perturbation), path relinking (population/elite hybrid).

*Uncertainty flags:* ILS has **no guarantee**; the big-valley hypothesis is an empirical regularity with partial theory. "Best performance-to-complexity ratio" and "chained LK is top-tier" are empirical (Lourenço–Martin–Stützle; Applegate et al.), instance- and tuning-dependent. The unifying equivalences (ILS↔SA-on-`S*`, VNS/GRASP as ILS variants) are conceptual framings, broadly accepted but not formal theorems in full generality.

