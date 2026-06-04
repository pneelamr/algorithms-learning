# Variable neighborhood search — Level 4: Undergraduate CS

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Clustering / p-median  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; systematic neighborhood changes
> **Used for:** Systematically switching neighborhood structures to balance diversify/intensify
> **Level 4 of 6** — undergrad: precise statement, the VNS variants, pseudocode, complexity, mermaid. See sibling files for other levels.

---

## Problem statement

Variable neighborhood search (VNS; Mladenović & Hansen 1997) is a **single-solution metaheuristic** built on one observation: a local optimum is defined **relative to a neighborhood**, so a point that is locally optimal under `N_a` may be improvable under `N_b`. VNS maintains an ordered set of neighborhoods `{N_1, …, N_{k_max}}` (usually nested/increasing in size) and **systematically changes** which one it uses to escape local optima.

## Three building blocks

- **A neighborhood ladder `N_1 ⊆ N_2 ⊆ … ⊆ N_{k_max}`** — e.g. for TSP: swap, 2-opt, Or-opt(1), Or-opt(2/3), double-bridge-scale shuffles.
- **Shake(s, k):** return a **random** solution in `N_k(s)` — diversification whose strength grows with `k`.
- **LocalSearch:** descend to a local optimum (often w.r.t. a small neighborhood, or a Variable Neighborhood **Descent**, below).

## The basic VNS loop

```
VNS(s, N[1..kmax], budget):
    s ← LocalSearch(s)
    while budget remains:
        k ← 1
        while k ≤ kmax:
            s'  ← Shake(s, k)            # random point in N_k(s)
            s'' ← LocalSearch(s')        # re-optimize
            if cost(s'') < cost(s):
                s ← s''; k ← 1           # improvement → intensify (reset ladder)
            else:
                k ← k + 1                # stuck → diversify (grow neighborhood)
    return s
```

The single rule — **reset `k=1` on improvement, increment `k` when stuck** — is the entire control logic, and it auto-balances intensification (small `k`) and diversification (large `k`).

## The VNS family

| Variant | Idea |
|---------|------|
| **Variable Neighborhood Descent (VND)** | *deterministic*: cycle through `N_1…N_{k_max}` as the **local search** itself — when `N_k` finds no improvement, try `N_{k+1}`; reset on success. A local optimum w.r.t. **all** neighborhoods. |
| **Reduced VNS (RVNS)** | Shake only, **no local search** — fast, for huge instances. |
| **Basic VNS** | Shake + local search (above). |
| **General VNS (GVNS)** | Basic VNS whose LocalSearch is a **VND** — the strong, standard form. |
| **Skewed VNS** | accept slightly-worse solutions if structurally distant (escape large flat regions). |

**VND** is the key sub-idea: it turns "change the neighborhood" into a *descent* that only stops at a point locally optimal under every neighborhood in the ladder — a much stronger local optimum than any single neighborhood gives.

## Pseudocode — General VNS (GVNS) for TSP

```
GVNS_TSP(V, d, N[1..kmax], budget):
    s ← localSearch_VND(construct(V,d))        # VND descent over {swap,2-opt,Or-opt,...}
    best ← s
    while budget remains:
        k ← 1
        while k ≤ kmax:
            s'  ← shake(s, k)                  # e.g. k successive random double bridges
            s'' ← localSearch_VND(s')
            if cost(s'') < cost(s): s ← s''; k ← 1
            else: k ← k + 1
        if cost(s) < cost(best): best ← s
    return best
```

## Complexity

- **Shake:** `O(k)`–`O(n)` (a few random moves).
- **LocalSearch / VND:** the dominant cost — one descent per shake; VND runs several neighborhood scans (`Θ(n²)` each for 2-opt/Or-opt) until no neighborhood improves.
- **Total:** `O(iterations · VND_cost)`; budget-driven. Space `O(n²)` matrix + `O(n)` working.

## Relationship to ILS (important)

VNS and **iterated local search** are very close — both **shake then re-optimize**. The difference is the **diversification policy**:
- **ILS:** a single, fixed perturbation (e.g. one double bridge), with adaptivity in the *acceptance* rule.
- **VNS:** a **systematic ladder** of perturbation strengths `N_1…N_{k_max}`, grown on failure and reset on success, with a (possibly VND) local search.

So VNS = ILS whose perturbation is a *structured, self-scaling* kick. Both are special cases of "search the local-optima landscape by escape-and-reoptimize."

## Control flow

```mermaid
flowchart TD
    S([s ← LocalSearch(construct)]) --> K["k ← 1"]
    K --> SH["Shake: s' ← random point in N_k(s)"]
    SH --> LS["LocalSearch / VND: s'' ← descend(s')"]
    LS --> IMP{"cost(s'') < cost(s)?"}
    IMP -- yes --> ACC["s ← s''; k ← 1 (intensify)"]
    IMP -- no --> INC["k ← k + 1 (diversify)"]
    ACC --> B{"budget left?"}
    INC --> KMAX{"k ≤ kmax?"}
    KMAX -- yes --> SH
    KMAX -- no --> B
    B -- yes --> K
    B -- no --> O([return best])
```

## Where it sits

VNS is the **neighborhood-changing** single-solution metaheuristic, exploiting that local optimality is neighborhood-relative. It is the systematic-perturbation cousin of **iterated local search**, contrasts with **tabu search** (memory) and **simulated annealing** (stochastic acceptance), and like all of them is built **on top of the local-search cluster's move sets** (2-opt, Or-opt). It is especially strong on **VRP, clustering, and p-median / facility-location**.

## One-line summary

Systematic escape by **changing the neighborhood**: shake in a growing ladder `N_1…N_{k_max}`, re-optimize (often by VND, which descends until locally optimal under *every* neighborhood), reset on improvement and grow on failure — a self-scaling cousin of iterated local search with no guarantee, strong on routing/clustering.

