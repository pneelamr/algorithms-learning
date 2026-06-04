# Ant colony optimization — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Network routing  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; often good on structured instances
> **Used for:** Pheromone-guided construction inspired by ant foraging
> **Level 5 of 6** — graduate: the ACO metaheuristic formalism, variants, model-based view, convergence, TSP practice. See sibling files for other levels.

---

## 1. The ACO metaheuristic, formally

ACO (Dorigo 1992; Dorigo & Stützle) solves problems mappable to building a solution as a path in a **construction graph** `G=(C,L)`: components `C` (here, edges of the TSP, or city-to-city transitions) carry pheromone `τ` and heuristic `η`. A solution is a feasible walk; ants sample walks under the **stochastic policy**

```
p(c_j | partial solution, i) = [τ_ij]^α [η_ij]^β / Σ_{l ∈ feasible} [τ_il]^α [η_il]^β,
```

and the algorithm iterates **ConstructAntSolutions → (optional LocalSearch) → UpdatePheromones**. The pheromone vector `τ` is the algorithm's **state**: a parameterized probability distribution over solutions, updated by reinforcement. This makes ACO a **model-based search** — it maintains and adapts an explicit generative model of good solutions, unlike the *instance-based* SA/local-search (which only ever hold concrete solutions).

## 2. The variant ladder (and why MMAS/ACS win)

| Variant | Who deposits | Key device | Effect |
|---------|--------------|-----------|--------|
| **Ant System (AS)** | all ants | basic evaporate+deposit | foundational, weak (slow, stagnation) |
| **Elitist AS** | all + extra for best-so-far | elitist reinforcement | faster convergence |
| **Rank-based AS** | top-`w` ranked ants | rank-weighted deposit | controlled pressure |
| **MAX–MIN AS (MMAS)** | **iteration-best / best-so-far only** | clamp `τ ∈ [τ_min, τ_max]`; init at `τ_max` | strong exploration floor; **best plain ACO for TSP** |
| **Ant Colony System (ACS)** | best-so-far (global) | `q0` exploit rule + **local pheromone decrease** on use | aggressive exploitation + diversification |

The two performant variants share a design insight: **restrict deposit to elite solutions** (intensify) while **protecting exploration** — MMAS by the `τ_min` floor and `τ_max` ceiling, ACS by lowering pheromone on freshly used edges so followers diverge. Both are explicit answers to ACO's central failure mode, **stagnation** (premature convergence of `τ` onto one tour).

## 3. The exploration/exploitation control surface

ACO's behavior is governed by a small set of couplings:
- **`α` vs `β`:** learned model vs static heuristic. High `α` accelerates reinforcement (risking stagnation); high `β` ≈ greedy construction (nearest-neighbor-like).
- **`ρ` (evaporation):** forgetting rate / model learning rate. Large `ρ` ⇒ fast adaptation but volatility; small `ρ` ⇒ long memory but lock-in.
- **`m` (ants):** Monte-Carlo sample size per model update — variance vs cost.
- **`q0` (ACS), `[τ_min,τ_max]` (MMAS):** explicit exploration safeguards.

This is the same explore/exploit tension as SA's temperature and the GA's selection-pressure-vs-diversity — ACO just exposes it through *model* parameters rather than acceptance or selection.

## 4. Convergence theory

- **Convergence in value / to the optimum:** for **ACS** and **MMAS** (with `τ_min > 0` bounded away from 0, or `τ_min` decreasing slowly), one can prove the best-so-far solution converges to a global optimum with probability → 1 as iterations → ∞ (Dorigo & Stützle; Gutjahr's *Graph-Based Ant System* convergence proofs). Intuition: a positive pheromone floor keeps every solution reachable, while elite reinforcement biases sampling toward the optimum.
- These are **asymptotic existence** results (the optimum is *found and remembered* eventually), **not** finite-time quality bounds — exactly parallel to SA's logarithmic-cooling guarantee. Practical ACO has **no guarantee**.

## 5. Model-based search and connections

Framing `τ` as a probability model links ACO to:
- **Estimation-of-distribution algorithms (EDAs):** both maintain/sample a distribution over solutions; ACO's update is a particular reinforcement rule. (Cross-link to the genetic-algorithm entry, where EDAs appear as the model-based successor to crossover.)
- **Reinforcement learning / policy-gradient:** the pheromone update is a reward-weighted reinforcement of solution components; ACO is often read as a stochastic policy improved by sampled returns — a precursor to modern **neural constructive** TSP solvers (pointer networks, attention models trained by RL), which replace `τ`/`η` with a learned neural policy.
- **Cross-entropy method:** another model-based-search cousin.

## 6. TSP practice

- **Construction cost** is `O(n²)` per ant; **candidate lists** (nearest-`cl` neighbors, `cl≈15–20`) make large `n` feasible and are mandatory at scale.
- **Local search is decisive.** Competitive ACO-TSP = **MMAS or ACS + 2-opt / 3-opt / Lin–Kernighan** applied to each ant's tour. The pheromone model then learns a *distribution over good local optima* — a population/model analogue of chained LK's single-trajectory restart and the GA's recombination over local optima. Without local search, ACO is uncompetitive with LK-class methods.
- **Quality:** hybrid MMAS+LK reaches a small percent over optimal on mid-size TSPLIB; still generally **below LKH / EAX-memetic** on the largest instances. ACO's comparative edge is on **dynamic / online routing** (below), not static Euclidean TSP.

## 7. Edge cases & strengths

- **Dynamic problems:** because `τ` is an *adaptive* model, ACO reacts gracefully to changing data — **network routing** (AntNet) and dynamic VRP are where ACO is genuinely strong, arguably its best niche.
- **Constraint handling:** infeasible transitions simply get zero probability (feasibility baked into `N_i`), making ACO natural for heavily-constrained construction (scheduling, sequencing).
- **Stagnation** is the main pathology; `[τ_min,τ_max]`, evaporation tuning, and pheromone re-initialization on stall are the standard defenses.
- **Parameter sensitivity** is high; self-adaptive / reactive ACO tunes `α,β,ρ,q0` online.

## 8. Synthesis

ACO is a **model-based, population, constructive** metaheuristic: ants sample tours from a pheromone-parameterized distribution `∝ τ^α η^β`, and **evaporation + elite reinforcement** steer that distribution toward short tours. Its performant forms (**MMAS, ACS**) tame stagnation with explicit exploration floors and, like every competitive metaheuristic in this cluster, **hybridize with local search** to evolve a distribution over *local optima*. It carries only asymptotic convergence guarantees (à la SA), loses to Lin–Kernighan-class methods on static TSP, but earns a distinctive niche on **dynamic/online routing** and a conceptual role as the bridge from classical metaheuristics to **model-based and learned (RL) constructive search**.

