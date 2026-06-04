# Tabu search — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Job-shop scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; avoids cycling
> **Used for:** Local search with memory of recent moves to avoid revisiting solutions
> **Level 6 of 6** — researcher: adaptive memory frontier, path relinking, hybrids, open problems, references. See sibling files for other levels.

---

## 1. Position: the adaptive-memory paradigm

Tabu search is the canonical **adaptive memory programming** metaheuristic and Fred Glover's central contribution to combinatorial optimization. Its influence is conceptual as much as algorithmic: the explicit **recency / frequency / quality / influence** memory taxonomy, the **intensification–diversification** vocabulary, and operators like **path relinking** and **scatter search** propagated across the whole metaheuristic field. On TSP it is not state of the art (Lin–Kernighan / EAX dominate), but on **quadratic assignment** and **scheduling** it remains a reference method, and its memory machinery is a standard component in hybrid solvers.

## 2. Theory frontier

- **No convergence/approximation guarantees**; tabu search's deterministic memory dynamics are analytically hard. The only clean property is **structural anti-cycling** (recency tenure `t` excludes attribute-cycles of period `≤ t`).
- **Dynamical-systems view:** the `(s, M)` trajectory is a deterministic dynamical system on an enlarged state space; characterizing its attractors, chaotic regimes, and the tenure thresholds separating cycling from ergodic wandering is largely open and motivates **reactive** schemes.
- **Memory as learning:** frequency/quality memory is a form of online learning about the search landscape; connecting it rigorously to model-based search (ACO/EDA) and to reinforcement learning is an active conceptual thread.

## 3. Key operators and hybrids

- **Path relinking** (Glover): generate trajectories *between* elite solutions by stepwise attribute exchange; a powerful intensification/recombination operator that bridges single-solution TS to population ideas — frequently paired with **GRASP** (GRASP+PR) and **scatter search**.
- **Scatter search:** a population method built on the same diversification/intensification and reference-set logic as TS.
- **Granular / candidate-list TS:** scalability via restricted neighborhoods (length-thresholded edges) — the TSP/VRP workhorse form (Toth–Vigo granular TS).
- **Reactive TS** (Battiti–Tecchiolli): self-tuning tenure; among the most practically important refinements.
- **TS + deep local search:** tabu memory layered over 3-opt/LK neighborhoods; tabu as the diversification controller inside larger hybrids.

## 4. Where it leads / domains

- **QAP:** robust tabu search (Taillard 1991) — flagship result.
- **Scheduling:** job-shop (Nowicki–Smutnicki's i-TSAB is a celebrated TS), flow-shop, timetabling.
- **Vehicle routing:** granular and unified tabu search; long a leading VRP metaheuristic family.
- **Telecommunications, graph problems, feature selection:** broad applicability wherever a move-attribute neighborhood exists.

## 5. Open problems / threads

1. **Convergence/dynamics theory** for tabu trajectories; principled tenure thresholds beyond reactive heuristics.
2. **Memory ↔ learning unification:** formal links among TS frequency memory, ACO pheromone, EDAs, and RL.
3. **Auto-configured TS:** learned tenure/attribute/aspiration policies generalizing across instances.
4. **Path-relinking theory:** when relinking provably improves over independent restarts.
5. **TS vs LK on structured TSP/VRP:** instance characterizations of comparative advantage.

## 6. References (seminal → modern)

- Glover, F. (1986). *Future paths for integer programming and links to artificial intelligence.* (Coins "tabu search".)
- Glover, F. (1989, 1990). *Tabu Search — Part I & II.* (The framework.)
- Glover & Laguna (1997). *Tabu Search* (the book).
- Taillard, É. (1991). *Robust taboo search for the quadratic assignment problem.*
- Battiti & Tecchiolli (1994). *The reactive tabu search.*
- Nowicki & Smutnicki (1996/2005). *A fast taboo search algorithm for the job-shop problem* (i-TSAB).
- Toth & Vigo (2003). *The granular tabu search and its application to the VRP.*
- Glover, Laguna & Martí (2000). *Fundamentals of scatter search and path relinking.*

## 7. Links to related problems

- **Inner neighborhood:** 2-opt / Or-opt / 3-opt (TSP); disjunctive-graph moves (scheduling); shares the local-search cluster's move sets.
- **Sibling metaheuristics (this registry):** simulated annealing (stochastic escape — the direct philosophical contrast), genetic / evolutionary algorithm and ant colony optimization (population), iterated local search (perturbation), variable neighborhood search (neighborhood change), GRASP (randomized restart; GRASP+path-relinking is a TS-flavored hybrid).
- **Operators:** path relinking / scatter search (bridge to population methods); candidate lists ↔ LK α-nearness.
- **Dominant on TSP instead:** Lin–Kernighan, chained Lin–Kernighan / LKH, EAX-memetic.

*Uncertainty flags:* tabu search has **no guarantee**; only anti-cycling is structural. "Top method on QAP/scheduling" and "below LK on TSP" are empirical, instance- and tuning-dependent. The memory-as-learning and dynamical-systems framings are active research perspectives, not settled theory.

