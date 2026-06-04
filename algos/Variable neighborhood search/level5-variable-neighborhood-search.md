# Variable neighborhood search — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Clustering / p-median  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; systematic neighborhood changes
> **Used for:** Systematically switching neighborhood structures to balance diversify/intensify
> **Level 5 of 6** — graduate: the principles, VND, the variant family, ILS relationship, practice. See sibling files for other levels.

---

## 1. The three principles (Hansen–Mladenović)

VNS rests on three observations about neighborhoods `{N_k}`:

1. **A local optimum w.r.t. `N_a` need not be local optimum w.r.t. `N_b`.** (Local optimality is neighborhood-relative.)
2. **A global optimum is local optimum w.r.t. *all* neighborhoods.** (So searching under many neighborhoods is necessary to certify quality.)
3. **For many problems, local optima w.r.t. several neighborhoods are *close* to each other** (the empirical big-valley-like regularity).

Principle 1 motivates **escaping by neighborhood change**; principle 2 motivates **VND** (descend until locally optimal under every `N_k`); principle 3 explains why a *small* shake `N_k` then re-optimization tends to land in a nearby, quality-correlated basin — the same big-valley exploitation ILS relies on.

## 2. Variable Neighborhood Descent (VND): stronger local optima

VND is the deterministic core. Given an ordered ladder `N_1, …, N_{k_max}` used as *improvement* neighborhoods:

```
VND(s):
    k ← 1
    while k ≤ k_max:
        s' ← bestImprovement in N_k(s)
        if cost(s') < cost(s): s ← s'; k ← 1     # restart ladder on success
        else: k ← k + 1
    return s                                       # local opt w.r.t. ALL N_k
```

The output is locally optimal under the **union** of neighborhoods — strictly stronger than any single-neighborhood optimum (cf. the k-opt ladder: a 3-opt optimum ⊆ 2-opt optimum; VND generalizes this "stack the neighborhoods" idea to arbitrary, heterogeneous move sets like {2-opt, Or-opt, swap}). **General VNS** = shaking + VND, the standard strong form.

## 3. The variant family as an intensify/diversify spectrum

```
RVNS  (shake only, no LS)        ── fastest, weakest        ── huge instances / quick bounds
Basic VNS (shake + single LS)    ── balanced
GVNS  (shake + VND)              ── strong, standard         ── VRP, p-median
VNDS  (decomposition VNS)        ── fix part, optimize a sub-problem of size k
Skewed VNS                        ── accept distant slightly-worse solutions (cross flats)
```

The ladder index `k` is the diversification dial: small `k` intensifies (re-optimize close to the incumbent), large `k` diversifies (bold shake). Unlike SA (temperature) and tabu search (tenure/memory), VNS's dial is **structural and systematic** — it *enumerates* perturbation strengths rather than tuning a continuous parameter, and it **adapts automatically** (reset on success, grow on failure) with essentially no parameters beyond the ladder itself. This near-parameter-freeness is VNS's signature selling point.

## 4. Relationship to ILS and the unified picture

VNS and **iterated local search** are the two "shake-and-reoptimize" walks on the local-optima landscape `S*`:

| | Iterated local search | Variable neighborhood search |
|---|---|---|
| perturbation | one **fixed** kick (double bridge), strength via acceptance/history | a **ladder** `N_1…N_{k_max}`, strength grown on failure, reset on success |
| local search | fixed (2-opt/LK) | often **VND** (multi-neighborhood descent) |
| diversification | acceptance criterion + (optional) adaptive kick | systematic neighborhood enumeration |

Both are instances of the cluster's master template *intensify-by-local-search / diversify-by-escape*; VNS's distinctive commitments are (i) **multiple neighborhoods** (in both shaking and descent) and (ii) a **deterministic, self-scaling** diversification schedule. ILS with an *adaptive, growing* perturbation effectively becomes VNS; VNS with a *single fixed* shake neighborhood and a single-neighborhood local search reduces to ILS.

## 5. Theory and guarantees

- **No approximation/convergence guarantee** in general (the cluster norm). 
- The **only structural property** is principle 2: at a VND optimum, no neighborhood in the ladder improves — a certificate relative to the chosen move set, not a global bound.
- VNS's practical strength is attributed to principle 3 (big-valley correlation) plus the robustness of near-parameter-free adaptive control; rigorous landscape theory tying neighborhood ladders to performance is open (shared with ILS).

## 6. Practice

- **Where VNS excels:** **p-median / facility location, clustering, VRP**, graph problems — domains with several natural, heterogeneous neighborhoods (swap/insert/interchange) where stacking them via VND is powerful. VNS is a top method on many of these.
- **TSP:** competitive with good local search; GVNS with 2-opt/Or-opt/double-bridge shakes is solid, but on pure Euclidean TSP **Lin–Kernighan / chained LK / EAX** still dominate. As with the whole cluster, the strong TSP form hybridizes with LK-class local search.
- **Engineering:** shares the local-search cluster's machinery (neighbor lists, `O(√n)` tour structures, don't-look bits); shakes are cheap, VND dominates cost.

## 7. Synthesis

Variable neighborhood search escapes local optima by **systematically changing the neighborhood**, grounded in three principles: local optimality is neighborhood-relative, a global optimum is optimal under all neighborhoods, and multi-neighborhood optima cluster. **VND** turns this into a descent yielding optima strong w.r.t. *every* move in a ladder; **(G)VNS** wraps VND in a self-scaling shake that resets on improvement and grows on failure — a near-parameter-free, deterministic-diversification cousin of **iterated local search**. With no guarantee but excellent robustness, it is a leading method on **routing, clustering, and p-median**, and the fourth distinct answer (after SA's thermal, tabu's memory, ILS's fixed kick) to the single-solution escape question — leaving **GRASP** (randomized restart) to complete the set.

