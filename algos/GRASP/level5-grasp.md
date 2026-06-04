# GRASP — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Set covering, Scheduling  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; multi-start greedy + local search
> **Used for:** Multi-start randomized-greedy construction followed by local search
> **Level 5 of 6** — graduate: construction theory, RCL tuning, path relinking, the cluster synthesis, practice. See sibling files for other levels.

---

## 1. GRASP as biased multi-start

Pure **multi-start** (random construction + local search, repeated) wastes effort polishing poor starts; pure **greedy** construction + local search gives one solution. GRASP interpolates: the **value-biased RCL** restricts construction to near-greedy choices while injecting enough randomness to make restarts **diverse but good**. Formally, each iteration samples a constructed solution from a distribution concentrated on high-quality region boundaries, then maps it through `LS` to a local optimum; GRASP returns `min` over an i.i.d. sample of `LS`-optima. The design question is the **bias–variance trade** of the RCL: low `α` ⇒ low variance, low diversity (restarts cluster); high `α` ⇒ high diversity, low average quality (junk to polish).

## 2. RCL design and α-tuning

- **Value-based RCL:** `{e : g(e) ≤ c_min + α(c_max − c_min)}`. **Cardinality-based RCL:** best `p` candidates. Hybrids exist.
- **Reactive GRASP** (Prais & Ribeiro): treat `α` as a random variable over a discrete set, updating each value's selection probability by the quality of solutions it produced — self-tuning the most sensitive parameter (parallel to reactive tabu search's adaptive tenure).
- **Construction–LS coupling:** the optimal `α` depends on the local search; a stronger `LS` (e.g. LK) tolerates more construction randomness (higher `α`) because it repairs more.
- **Cost-perturbation / sampling variants:** randomize `g` itself rather than the selection.

## 3. Path relinking: from memoryless to memory

GRASP's defining limitation is **independence** of iterations — no learning across restarts (unlike ACO's pheromone, ILS's incumbent reuse, or tabu's memory). **Path relinking (PR)** (Glover; Laguna–Martí for GRASP) supplies memory:
- Maintain an **elite set** `E` of diverse high-quality solutions.
- For a new local optimum `s` and a guide `t ∈ E`, generate the **path** `s = s_0, s_1, …, s_k = t` by, at each step, applying the move that most reduces the symmetric difference to `t`; record the best solution on the path.
- Update `E` with quality+diversity criteria.

PR is an **intensification by recombination of attributes** — conceptually a structured, deterministic crossover between elite solutions, linking GRASP to scatter search, tabu search, and (loosely) the GA's recombination. **GRASP+PR** is the standard competitive form; without PR, GRASP is a robust but unremarkable baseline.

## 4. Cluster synthesis — GRASP as the last piece of the escape taxonomy

GRASP completes the metaheuristics cluster. All members attack the same problem — **escape local optima / search the landscape of local optima** — via *intensify with local search, diversify with some escape operator*. The escape operators are the whole story:

| Metaheuristic | Type | Escape / diversification mechanism |
|---------------|------|-------------------------------------|
| Simulated annealing | single-solution | probabilistic uphill acceptance `e^{−Δ/T}` (thermal) |
| **Tabu search** | single-solution | adaptive **memory** (forbid recent reversals) |
| **Iterated local search** | single-solution | **perturb** the incumbent (double-bridge kick) |
| **Variable neighborhood search** | single-solution | systematic **neighborhood change** (ladder) |
| **GRASP** | single-solution (multi-start) | **independent randomized-greedy restart** |
| Genetic / evolutionary | population | **recombination** (crossover) |
| Ant colony optimization | population | **pheromone model** (learned construction bias) |

GRASP is the **memoryless restart** corner of this space; **path relinking** moves it toward the memory/recombination corner. Read across the table, the cluster is a small set of orthogonal answers — acceptance, memory, perturbation, neighborhood, restart, recombination, learned-model — to one question, with **local search as the shared intensifier** and (for TSP) **Lin–Kernighan-class methods as the common high-performance core** every variant hybridizes with.

## 5. Theory and guarantees

- **No approximation/convergence guarantee** in finite time. An **asymptotic** observation: as `maxIter → ∞`, GRASP's probability of constructing (hence polishing toward) an optimal-containing region → 1 if every solution has positive construction probability (`α > 0`) — a multi-start analogue of SA's asymptotic optimality, equally impractical.
- **Time-to-target (TTT) plots** (Aiex–Resende–Ribeiro): GRASP's running time to reach a target value is well-modeled as an **exponential distribution**, which (i) explains why **independent parallelization gives near-linear speedup** and (ii) is a standard empirical analysis tool for restart metaheuristics.

## 6. Practice

- **Where GRASP shines:** problems with a natural greedy + local search and a need for simplicity/parallelism — **set covering, scheduling, assignment, location, routing**. GRASP+PR is competitive on many.
- **TSP:** GRASP (randomized greedy/space-filling construction + 2-opt/Or-opt, or + LK) is solid but **below chained LK / EAX**; as everywhere in this cluster, the strong TSP form uses LK-class local search.
- **Parallelism:** the cleanest of the cluster — independent iterations, optional shared elite pool for PR; near-linear speedups per TTT theory.

## 7. Synthesis

GRASP is **biased randomized multi-start**: a Restricted Candidate List makes each construction near-greedy yet diverse, local search polishes it, and the best over many independent (parallel) restarts is returned — memoryless by default, made competitive by **path relinking**, which injects cross-iteration learning via elite-solution recombination. As the final metaheuristic of the registry, it completes a compact taxonomy in which **acceptance (SA), memory (tabu), perturbation (ILS), neighborhood change (VNS), restart (GRASP), recombination (GA), and learned models (ACO)** are seven orthogonal escape mechanisms over a shared local-search intensifier — and, on TSP, over the shared Lin–Kernighan core.

