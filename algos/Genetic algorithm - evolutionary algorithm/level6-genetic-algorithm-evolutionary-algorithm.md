# Genetic algorithm / evolutionary algorithm — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Job scheduling, Knapsack / feature selection, Hyperparameter / architecture search  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Population-based global search via selection, crossover, and mutation
> **Level 6 of 6** — researcher: EAX state of the art, runtime theory, EA taxonomy, open problems, references. See sibling files for other levels.

---

## 1. Position: population search, and TSP's evolutionary high-water mark

Genetic/evolutionary algorithms are the **population-based** branch of metaheuristics. On TSP their relevance is sharply bimodal: the **plain GA is uncompetitive** (beaten by 2-opt-seeded local search), while the **EAX memetic algorithm** is one of very few methods that **rival LKH** on large instances. The research story is therefore mostly about (i) **edge-respecting recombination**, (ii) the **memetic** fusion with Lin–Kernighan-class local search, and (iii) **diversity control**. As a general optimizer, the EA framework remains a default across domains where solutions are easy to score and recombine but hard to search directly.

## 2. EAX and the TSP state of the art

- **EAX** (Nagata & Kobayashi 1997; Nagata 2006; Nagata & Kobayashi 2013) builds children by decomposing `E_A △ E_B` into **AB-cycles**, assembling intermediates, and repairing subtours — aggressively inheriting parent edges. Combined with a **distance-preserving population** (children replace parents only if they improve fitness *and* maintain diversity), EAX-GA solves many TSPLIB instances to optimality and reaches within tiny fractions of a percent on instances up to ~100k+ cities — **competitive with, sometimes exceeding, LKH** on specific large/structured instances.
- The lesson generalized: **the crossover operator is where domain knowledge lives.** EAX encodes "preserve good edges, introduce few short new ones" — the evolutionary analogue of LK's α-nearness candidate edges (Held–Karp 1-tree), tying back to the exact-methods cluster.

## 3. Evolutionary-computation theory frontier

- **Runtime analysis** (drift analysis, fitness-level method, switch analysis): rigorous expected-runtime bounds for simple EAs — `(1+1)`-EA on OneMax `Θ(n log n)`, on LeadingOnes `Θ(n²)` — and increasingly for crossover-based EAs (proving crossover *provably* helps on jump functions, royal-roads). These illuminate *when recombination helps* but don't reach EAX-memetic TSP.
- **Black-box complexity** lower-bounds what *any* EA-like algorithm can achieve on a problem class.
- **Building-block hypothesis** remains formally contested; modern theory (linkage learning, **estimation-of-distribution algorithms** that replace crossover with a learned probabilistic model) partly supersedes it.
- **No-Free-Lunch** (Wolpert–Macready) frames why operator/representation choice (e.g. edge-based for TSP) is essential — no universally best search.

## 4. The EA taxonomy and modern variants

- **Evolution strategies / CMA-ES** (Hansen): self-adaptive continuous optimization; dominant for black-box continuous and a backbone of **hyperparameter / architecture search**.
- **Estimation-of-distribution algorithms (EDAs):** learn and sample a distribution over good solutions instead of crossover.
- **Genetic programming:** evolve programs/expressions.
- **Differential evolution / particle-swarm:** other population dynamics for continuous spaces.
- **Quality-Diversity (MAP-Elites, novelty search):** optimize for a *diverse archive* of high performers — a modern reframing of diversity from nuisance to objective.
- **Neuroevolution & learned operators:** evolve neural nets; learn crossover/mutation policies (2020s).

## 5. Applications beyond TSP

Job-shop/flow-shop scheduling, knapsack & **feature selection**, vehicle routing, circuit/antenna design, **hyperparameter optimization and neural architecture search** (regularized evolution rivaling RL-based NAS), drug/molecule design, and multi-objective optimization (**NSGA-II/III** — Pareto-front evolution) are core EA territory. The framework's reach, not its TSP performance, is its main claim.

## 6. Open problems / threads

1. **Runtime theory for memetic/crossover EAs** on combinatorial problems approaching EAX-TSP.
2. **Principled crossover design** from problem structure (EAX-style) — automating "where domain knowledge enters."
3. **Diversity/quality trade-off theory:** optimal diversity-preservation for population search (formalizing why distance-preserving EAX works).
4. **Learned operators with guarantees:** RL/amortized crossover & mutation that generalize across instances.
5. **EA vs. LK on TSP:** characterize the instance classes where EAX provably beats (or loses to) chained LK.

## 7. References (seminal → modern)

- Holland, J. (1975). *Adaptation in Natural and Artificial Systems.* (GAs, schema theorem.)
- Goldberg, D. (1989). *Genetic Algorithms in Search, Optimization, and Machine Learning.* (Building blocks; canonical text.)
- Whitley, Starkweather & Fuquay (1989). *Scheduling problems and traveling salesmen: the genetic edge recombination operator (ERX).*
- Nagata & Kobayashi (1997; 2013). *Edge Assembly Crossover (EAX)* and *A powerful GA using EAX for the TSP.* (TSP state of the art.)
- Moscato, P. (1989). *On evolution, search, optimization, GAs and martial arts* (memetic algorithms).
- Wolpert & Macready (1997). *No free lunch theorems for optimization.*
- Hansen & Ostermeier (2001). *Completely derandomized self-adaptation in evolution strategies (CMA-ES).*
- Deb et al. (2002). *NSGA-II.* (Multi-objective EA.)

## 8. Links to related problems

- **Inner local search (memetic):** 2-opt, Or-opt, Lin–Kernighan, chained Lin–Kernighan — MA evolves over their local optima; recombination is the population analogue of the double-bridge kick.
- **Sibling metaheuristics (this registry):** simulated annealing (single-trajectory, thermal escape), ant colony optimization (population-based but pheromone-constructive), tabu search / GRASP / VNS (single-solution escape).
- **Cross-cluster:** EAX edge-inheritance ↔ α-nearness / Held–Karp 1-tree candidate edges; LKH as the local-search engine inside memetic TSP.
- **Cross-domain:** CMA-ES and regularized evolution for hyperparameter / architecture search; NSGA-II for multi-objective; EDAs / quality-diversity.

*Uncertainty flags:* "EAX competitive with LKH" is empirical and instance-class dependent (Nagata; DIMACS-style comparisons) — neither dominates universally. Schema-theorem/building-block claims are foundational intuition, formally contested, and do not transfer cleanly to permutation TSP. Runtime-analysis results are rigorous only for simplified EAs, not memetic EAX. Numeric quality/scale figures are representative, not guarantees.

