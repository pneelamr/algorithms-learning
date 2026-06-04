# Tabu search — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Job-shop scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; avoids cycling
> **Used for:** Local search with memory of recent moves to avoid revisiting solutions
> **Level 5 of 6** — graduate: memory formalism, intensification/diversification, reactive TS, theory, practice. See sibling files for other levels.

---

## 1. Adaptive memory programming

Tabu search (Glover 1986, 1989/90) is the prototypical **adaptive memory** metaheuristic. Its state is the pair `(s, M)` where `M` is structured memory; the policy is *deterministic given `M`* (in contrast to SA's stochastic acceptance). Memory has four classically-named dimensions:
- **Recency** (short-term): the tabu list — recently-changed attributes are frozen for a **tenure**.
- **Frequency** (long-term): how often attributes appear in visited solutions — drives diversification/intensification penalties.
- **Quality:** memory of elite solutions/attributes to reinforce.
- **Influence:** memory of which moves caused large structural changes.

The defining move rule: `s_{t+1} = argmin_{s' ∈ N(s_t)} { cost(s') : admissible(s', M_t) }`, where *admissible* = not tabu **or** satisfies an **aspiration** criterion. There is no termination at local optima — the trajectory is a memory-guided walk over `S`, and the output is the incumbent best.

## 2. Why memory, not randomness

Tabu search and simulated annealing solve the **same problem** (escape local optima without cycling) with opposite philosophies:

| | Simulated annealing | Tabu search |
|---|---|---|
| escape | accept worse w.p. `e^{−Δ/T}` | take best **non-tabu** move (possibly worse) |
| anti-cycling | statistical (rarely returns) | **structural** (tabu list forbids return) |
| determinism | stochastic | deterministic given memory |
| control | temperature schedule | tenure + aspiration + frequency memory |

The structural anti-cycling is tabu search's theoretical core: by forbidding reversal of recent attribute changes for `t` steps, any cycle of length `≤ t` in attribute space is **impossible**, guaranteeing the trajectory cannot short-cycle (though longer cycles and chaotic wandering remain possible — hence reactive tenure).

## 3. Intensification vs. diversification — made explicit

Tabu search is where the **intensify/diversify** dial (implicit in SA's temperature, the GA's selection-pressure-vs-diversity, ACO's `α/ρ`) becomes an *explicit, memory-driven* control:
- **Intensification:** return to and search around elite solutions; reduce tenure; reward elite attributes. (Exploit known-good regions.)
- **Diversification:** **frequency-based penalties** `cost'(s') = cost(s') + λ·freq(attributes)` push the search toward rarely-used attributes; periodic restarts from diversified points. (Explore new regions.)

This explicit two-memory architecture is tabu search's lasting conceptual contribution to metaheuristics.

## 4. Reactive and advanced variants

- **Reactive Tabu Search** (Battiti & Tecchiolli 1994): detect solution repetitions (via hashing) and **adapt the tenure** automatically — increase on repetition, decay otherwise — removing the most sensitive hand-tuned parameter.
- **Robust/strict tabu, tabu thresholding, path relinking:** Glover's extensions; **path relinking** explores trajectories *between* elite solutions and is a powerful intensification operator (also used to hybridize with GRASP and scatter search).
- **Granular / candidate-list tabu:** restrict `N(s)` to promising moves (e.g. edges within a length threshold) for scalability — the analogue of LK's candidate lists.
- **Probabilistic tabu:** sample among good admissible moves rather than strict argmin.

## 5. Theory and guarantees

- **No approximation or convergence guarantee** in general; tabu search is a heuristic. Unlike SA (asymptotic optimality under logarithmic cooling) or ACS/MMAS (asymptotic convergence), tabu search's deterministic memory dynamics resist clean convergence theory; results are largely empirical.
- **Anti-cycling** is its one structural property: tenure-`t` recency memory excludes attribute-cycles of period `≤ t`. This is a finite, local guarantee about the *trajectory*, not the *solution quality*.
- Behavior is a complex function of `(tenure, attribute definition, aspiration, frequency penalties)`; reactive schemes exist precisely because this surface is hard to set a priori.

## 6. Practice: where tabu search dominates

- **Quadratic assignment problem (QAP):** robust tabu search (Taillard) is historically among the best QAP methods — TS's flagship domain.
- **Job-shop / flow-shop scheduling:** TS is a top-tier approach; disjunctive-graph neighborhoods + tabu memory are standard.
- **Vehicle routing:** granular tabu search and unified TS frameworks are strong, widely deployed.
- **TSP specifically:** TS is competitive with 2-opt/3-opt-based local search but generally **below Lin–Kernighan / chained LK / EAX**. TSP is not TS's comparative strength; its edge is on **assignment and scheduling** structures where LK-style moves don't exist.

## 7. Synthesis

Tabu search is **local search governed by adaptive memory**: always take the best admissible neighbor, forbid reversing recent attribute changes (recency/tabu list), override via aspiration, and steer the global trajectory with **frequency-based diversification** and **elite-based intensification**. It is the deterministic, structural counterpart to simulated annealing's stochastic escape, and it makes the intensify/diversify dial an explicit memory mechanism. Carrying only an anti-cycling property and no quality guarantee, it is nonetheless a top method on **QAP and scheduling**, while on TSP it cedes to Lin–Kernighan-class search. As the memory-based member of the single-solution metaheuristics, it sits beside **iterated local search** (perturbation), **variable neighborhood search** (neighborhood change), and **GRASP** (randomized restart) — four answers to the one escape question.

