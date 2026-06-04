# Iterated local search — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; layered on local search
> **Used for:** Repeatedly perturbing then re-optimizing a local optimum
> **Level 5 of 6** — graduate: the local-optima walk, perturbation theory, acceptance, the unifying view, practice. See sibling files for other levels.

---

## 1. Formal framing: a walk on `S*`

Let `LS: S → S*` map a solution to its local optimum under neighborhood `N`. ILS performs a stochastic walk **on the set of local optima** `S* = LS(S)`:

```
s*_{t+1} = Accept( s*_t,  LS(Perturb(s*_t)),  history ).
```

It never operates in the full space `S` except through `LS`. This is the crucial abstraction: ILS replaces the exponential, rugged landscape of all solutions with the smaller, smoother landscape of **local optima**, and explores *that* by perturb-then-reoptimize. Its quality ceiling is set by the quality of `S*` (i.e. by `LS`) and by how well `Perturb`/`Accept` traverse `S*`.

## 2. Perturbation theory: escape vs. correlation

The perturbation defines a transition kernel on `S*`. Effectiveness requires two competing properties:
- **Escape:** `LS(Perturb(s*)) ≠ s*` with good probability — the kick must leave the basin of attraction of `s*` under `LS`. A perturbation inside the basin is wasted (LS returns to `s*`).
- **Correlation (locality):** `Perturb(s*)` should remain close to `s*` so that `LS(Perturb(s*))` lands in a *nearby, quality-correlated* basin, exploiting the **big-valley** structure of `S*` (good local optima cluster near each other and near the global optimum).

The **double bridge** for TSP is the canonical solution: a 4-opt move that (i) is **non-sequential**, hence outside the 2-opt/3-opt/LK move set → reliable escape, and (ii) alters only 4 edges, preserving 4 long segments → high correlation. Perturbation *strength* is the key tunable: fixed double bridge, multiple kicks, or **adaptive strength** (increase on stagnation) — too small wastes iterations, too large → random restart (decorrelated `S*` walk).

## 3. Acceptance criteria and the LSMC view

`Accept` sets the walk's exploration/exploitation:
- **Better-only:** descent on `S*` — fast, can stagnate in a region of `S*`.
- **Random walk:** diffuse on `S*` — needs elitism to retain best.
- **Restart-threshold / better-with-restart:** bounded drift; the classic chained-LK choice.
- **Metropolis** `min(1, e^{−Δ/T})`: ILS becomes a **large-step Markov chain (LSMC)** — simulated annealing whose "moves" are *kick + local search* on `S*` rather than single moves on `S` (Martin–Otto–Felten 1991). This is the precise sense in which ILS unifies with SA: **SA at the meta-level of local optima.** The acceptance "temperature" here governs barrier crossing between basins of `S*`, not between adjacent solutions.

## 4. The unifying view of the cluster

ILS exposes the shared skeleton of the single-solution metaheuristics — *intensify with local search, diversify with a controlled escape* — and each sibling is a choice of escape operator on `S*`:

| Method | Escape operator on the local-optima landscape |
|--------|-----------------------------------------------|
| **ILS** | **perturbation** of the incumbent (e.g. double bridge) |
| Simulated annealing | probabilistic uphill acceptance (single moves; ILS-Metropolis lifts this to `S*`) |
| Tabu search | memory forbidding recent reversals |
| Variable neighborhood search | **systematic change of neighborhood** (perturb by switching `N_k`) |
| GRASP | **independent randomized-greedy restart** (no incumbent reuse) |

ILS and VNS are the closest pair — both perturb-and-reoptimize — differing in whether the diversification comes from a *fixed kick* (ILS) or a *growing neighborhood ladder* (VNS). GRASP is "ILS with the memory removed": restart instead of perturb.

## 5. History and adaptivity

The optional `history` argument turns ILS adaptive:
- **Adaptive perturbation strength:** grow the kick (e.g. successive double bridges) when stuck, shrink when improving — a bridge toward VNS's neighborhood ladder.
- **Acceptance annealing:** lower the Metropolis temperature over time.
- **Restart memory / elite pools:** combine with path relinking (the tabu-search/GRASP hybrid operator) to recombine elite local optima — ILS edging toward population methods.

## 6. Practice and quality

- **TSP:** ILS with **Lin–Kernighan** + double bridge = **chained LK**, one of the best known methods, reaching `< 0.1%` of optimal and scaling to `10⁶`–`10⁷` cities (Applegate–Cook–Rohe; in Concorde and LKH). ILS with mere 2-opt is already markedly better than 2-opt alone.
- **Scheduling / QAP:** ILS is a strong, simple baseline, often competitive with far more complex methods — its appeal is the **maximum performance-to-complexity ratio** among metaheuristics.
- **No guarantee:** like the whole cluster; warm-restart engineering (don't-look bits, `O(√n)` tour structures) is what makes the iteration count affordable.

## 7. Synthesis

Iterated local search is the minimal, unifying single-solution metaheuristic: a **stochastic walk on the landscape of local optima**, driven by a **perturbation** strong enough to escape a basin yet correlated enough to exploit big-valley structure, and an **acceptance** rule that — in its Metropolis form — reveals ILS as simulated annealing lifted to that landscape. It subsumes **chained Lin–Kernighan** as a special case, neighbors **variable neighborhood search** (escape by neighborhood change) and **GRASP** (escape by restart), and delivers top-tier TSP quality from an almost trivial template — the clearest statement of the cluster's common idea: *intensify by local search, diversify by controlled escape.*

