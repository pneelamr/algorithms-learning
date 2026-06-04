# GRASP — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Set covering, Scheduling  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; multi-start greedy + local search
> **Used for:** Multi-start randomized-greedy construction followed by local search
> **Level 6 of 6** — researcher: GRASP+PR, time-to-target theory, the cluster capstone, open problems, references. See sibling files for other levels.

---

## 1. Position: the simplest competitive multi-start, and the registry capstone

GRASP is the **multi-start** metaheuristic and the simplest of the cluster to implement, analyze, and parallelize. Its research arc is dominated by two ideas: **path relinking** (adding memory/recombination to the memoryless restart) and **time-to-target analysis** (the empirical theory that justifies its parallel scalability). On TSP it is not state of the art (Lin–Kernighan / EAX lead), but as a general framework — and especially **GRASP+PR** — it is a strong, widely-used method across covering, scheduling, location, and routing. As the final entry of this registry, GRASP closes the metaheuristics cluster and, with it, the heuristic half of the TSP toolbox.

## 2. GRASP + path relinking

- **Path relinking** (Glover 1997; GRASP+PR: Laguna & Martí 1999; Resende & Ribeiro) turns independent restarts into a learning search: an **elite reference set** guides each new local optimum along an attribute-exchange path, harvesting the best intermediate. It is GRASP's intensification engine and the bridge to **scatter search** (a population method on the same reference-set/PR logic).
- Variants: **forward / backward / mixed / truncated / greedy randomized** PR; **evolutionary PR** (PR within an evolving elite pool). GRASP+evPR blurs into population search.

## 3. Time-to-target theory and parallelization

- **TTT plots** (Aiex, Resende & Ribeiro 2002, 2007): the time for GRASP to reach a target objective value fits a **(shifted) exponential distribution**. Consequence: running `p` independent GRASPs gives **near-linear speedup** (probability of not hitting the target decays as the `p`-th power), making GRASP the cluster's best-understood and best-scaling method for **independent parallelism**. TTT plots are now a standard tool for comparing *any* restart/stochastic metaheuristic.
- This is the closest the cluster comes to a clean quantitative theory of running time — for the *time-to-target*, not for solution-quality guarantees.

## 4. The cluster capstone (what the whole metaheuristics arc says)

GRASP completes a 7-member taxonomy whose unifying thesis is: **metaheuristics = a local-search intensifier + one escape/diversification mechanism.**

```
escape mechanism            method                 reuse of past?
  thermal acceptance        simulated annealing    incumbent only
  adaptive memory           tabu search            short/long-term memory
  perturbation              iterated local search  incumbent (+ chained LK)
  neighborhood change       variable nbhd search   incumbent
  randomized restart        GRASP                  none (PR adds elite memory)
  recombination             genetic / memetic      population
  learned construction      ant colony optimization pheromone model
```

Cross-cutting facts established across the cluster:
- **Every competitive TSP form hybridizes with the local-search cluster** (2-opt/Or-opt/LK); the population/restart/perturbation logic is the *outer* loop.
- **The strongest TSP methods overall** are **chained Lin–Kernighan (= ILS+LK)** and **EAX memetic GA** — both *outer escape + LK-class inner search*.
- **Asymptotic-only guarantees** exist for SA (logarithmic cooling), ACS/MMAS (positive pheromone floor), and GRASP (positive construction probability); none give finite-time quality bounds. Tabu/ILS/VNS carry only structural properties.
- These heuristics produce the **upper bounds** that drive the **exact** cluster: chained-LK/EAX tours feed **Concorde**'s branch-and-cut pruning, sandwiching the optimum with the **Held–Karp** lower bound.

## 5. Open problems / threads

1. **Finite-time quality guarantees** for GRASP/GRASP+PR beyond asymptotics and TTT.
2. **Reactive/learned RCL** and learned construction (the bridge from GRASP's memoryless greed to ACO's learned bias and to neural constructive solvers).
3. **Path-relinking theory:** when PR provably beats independent restarts; optimal elite-set management.
4. **Unification:** formal placement of GRASP, ILS, VNS, SA, tabu within one framework (escape-operator algebra over `S*`).
5. **Parallel/distributed GRASP+PR** with shared elite pools at scale.

## 6. References (seminal → modern)

- Feo & Resende (1989, 1995). *A probabilistic heuristic for a computationally difficult set covering problem* / *Greedy randomized adaptive search procedures.*
- Resende & Ribeiro (2016). *Optimization by GRASP: Greedy Randomized Adaptive Search Procedures* (the book).
- Laguna & Martí (1999). *GRASP and path relinking for 2-layer straight line crossing minimization* (GRASP+PR).
- Aiex, Resende & Ribeiro (2002, 2007). *Probability distribution of solution time in GRASP* / *TTT plots.*
- Prais & Ribeiro (2000). *Reactive GRASP.*
- Glover (1997); Glover, Laguna & Martí (2000). *Path relinking and scatter search.*

## 7. Links to related problems

- **Construction ↔ constructive cluster:** randomized **nearest neighbor / greedy-edge / insertion**; the **memoryless** cousin of **ant colony optimization** (learned construction) and the multi-start sibling of **random / farthest insertion** seed ensembles.
- **Sibling metaheuristics (this registry):** iterated local search (GRASP = ILS without incumbent reuse; PR restores memory), variable neighborhood search, tabu search (path relinking is shared), simulated annealing, genetic / evolutionary (scatter search ≈ GRASP+PR as population method).
- **Inner local search:** 2-opt, Or-opt, Lin–Kernighan (the intensifier).
- **Cross-cluster:** GRASP/PR upper bounds, like all heuristic tours, feed **Concorde branch-and-cut**; **Held–Karp** lower bound completes the optimality sandwich.

*Uncertainty flags:* GRASP has **no finite-time guarantee**; only asymptotic construction-coverage and empirical TTT (exponential) results hold. "Below LK/EAX on TSP" and "GRASP+PR competitive on covering/scheduling/routing" are empirical and instance-dependent. The 7-mechanism taxonomy is a pedagogical synthesis (widely supported) rather than a formal classification theorem.

