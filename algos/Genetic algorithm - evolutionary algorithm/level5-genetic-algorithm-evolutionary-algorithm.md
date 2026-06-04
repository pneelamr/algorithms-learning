# Genetic algorithm / evolutionary algorithm — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Job scheduling, Knapsack / feature selection, Hyperparameter / architecture search  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Population-based global search via selection, crossover, and mutation
> **Level 5 of 6** — graduate: the schema theorem, building blocks, EA taxonomy, memetic search, diversity, TSP theory. See sibling files for other levels.

---

## 1. The evolutionary-algorithm framework

An EA is a stochastic search over a population `P ⊂ Ω` driven by **variation** (crossover + mutation) and **selection**. The umbrella spans:
- **Genetic algorithms (GA):** historically bit-strings; here, permutations. Crossover-centric.
- **Evolution strategies (ES):** continuous `ℝ^d`, **self-adaptive** mutation (e.g. CMA-ES adapts the covariance of Gaussian mutations); mutation-centric.
- **Evolutionary programming / genetic programming / differential evolution:** other encodings/operators.

Common abstraction: `P_{t+1} = Select(Variation(P_t))`, with the design degrees of freedom being **representation, fitness, selection pressure, variation operators, replacement,** and **diversity control**.

## 2. The schema theorem and the building-block hypothesis

Holland's classical theory (for binary GAs). A **schema** `H` is a template with fixed and wildcard (`*`) positions; its **order** `o(H)` = number of fixed bits, **defining length** `δ(H)` = span between outermost fixed bits. With fitness-proportional selection, one-point crossover (rate `p_c`), and mutation (rate `p_m`):

```
E[m(H, t+1)]  ≥  m(H,t) · (f(H)/f̄) · [1 − p_c·δ(H)/(ℓ−1) − o(H)·p_m]
```

**Schema theorem:** short, low-order, above-average-fitness schemata receive **exponentially increasing** trials over generations. The **building-block hypothesis** posits GAs work by assembling such short, fit schemata into solutions via crossover.

**Caveats (important):** the bound is a one-generation inequality, not a convergence proof; it assumes binary encoding and one-point crossover; the building-block hypothesis is **contested** (deceptive functions, the role of linkage). For **permutation** problems like TSP the classical schema analysis **does not transfer cleanly** — which is exactly why TSP needs edge-based crossovers (below) rather than the bit-string theory. Treat the schema theorem as foundational intuition, not a guarantee.

## 3. Why TSP needs edge-based recombination

The TSP objective depends on the **edge set** of the tour, not on absolute or relative city positions. Position-preserving crossovers (PMX, CX) and order-preserving ones (OX) inherit the *wrong* invariant, so they recombine poorly. The fix is to make the **schema = a set of edges**:
- **Edge Recombination (ERX, Whitley):** build a union adjacency list from both parents; construct the child by always extending to the neighbor with the fewest remaining adjacencies — maximizing inherited edges, minimizing "foreign" edges.
- **Edge Assembly Crossover (EAX, Nagata & Kobayashi):** form `E_A ∪ E_B`, decompose the symmetric difference into **AB-cycles** (alternating parent-A/parent-B edges), assemble an intermediate by toggling a subset of cycles, then repair subtours into a single Hamiltonian cycle. EAX preserves parent edges aggressively while introducing minimal new short edges — the strongest known TSP crossover.

The general principle: **the recombination operator must respect the problem's relevant substructure** (edges for TSP). This is the permutation-domain replacement for the building-block hypothesis.

## 4. Memetic algorithms: evolving over local optima

Plain GAs converge slowly and to mediocre tours on TSP. The dominant paradigm is the **memetic algorithm (MA)** (Moscato): apply **local search** (2-opt, Or-opt, **Lin–Kernighan**) to every individual, so the population lives on the manifold of **local optima**. Then:
- **Crossover** is the *escape/diversification* operator across basins — the population analogue of chained LK's double-bridge perturbation, but **biparental** (it recombines two good local optima rather than perturbing one).
- **Local search** is the *intensification* operator.

This makes MA and chained LK two faces of the same "search the space of local optima" idea: chained LK uses **unary perturbation + acceptance**; MA uses **binary recombination + selection** over a population. EAX-based MAs (Nagata) reach optimal or near-optimal on instances with tens of thousands of cities, **competitive with LKH** — the high-water mark for evolutionary TSP.

## 5. Selection pressure, diversity, and premature convergence

The central tension:
- **Selection pressure** (tournament size `k`, fitness scaling) accelerates convergence but risks **premature convergence** — the population collapses to one basin, losing the diversity crossover needs.
- **Diversity preservation** counteracts this: *fitness sharing/niching*, *crowding*, *restricted mating*, *island models* (subpopulations with occasional migration), and explicit **distance-preserving** replacement (e.g. EAX-MA keeps children only if they add diversity). 
- **Takeover time** (Goldberg–Deb) quantifies how fast the best individual saturates the population under a selection scheme — a design lever for the pressure/diversity balance.

Diversity management is *the* practical determinant of EA success and the main thing that separates a working memetic TSP solver from a collapsing one.

## 6. Theory vs. practice; no guarantee

- **Convergence results** exist for restricted EAs (e.g. `(1+1)`-EA runtime analysis on benchmark functions; elitist EAs converge to the optimum in the limit with positive mutation probability), but they do **not** yield practical guarantees for TSP GAs.
- **Runtime analysis** of EAs is a rigorous subfield (drift analysis, fitness levels) — but its tractable results concern simple functions/operators, not EAX-memetic TSP.
- Net: GA/EA is a **heuristic with no guarantee**; its TSP relevance is empirical and concentrated in the memetic/EAX form.

## 7. Synthesis

A genetic/evolutionary algorithm searches by **selection + variation over a population**, with recombination as the distinctive escape mechanism. The classical **schema/building-block** theory motivates it but does not transfer to permutation TSP, where success demands **edge-based crossover** (ERX, EAX) and, decisively, the **memetic** marriage to local search — making the population evolve over **local optima**, the same space chained Lin–Kernighan explores by perturbation. Balanced against **premature convergence** via diversity control, EAX-based memetic algorithms reach LKH-competitive quality on large TSP, while the plain GA remains a general-purpose, guarantee-free baseline whose real strength is cross-domain applicability.

