# Greedy edge-selection (cheapest link) — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Circuit / cable layout  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; depends on instance
> **Used for:** Constructing a tour by adding cheapest valid edges first
> **Level 5 of 6** — graduate: formal model, the matroid-intersection view, the Θ(log n) bound, edge cases, optimizations. See sibling files for other levels.

---

## 1. Formal setting and the constraint structure

Metric TSP on `(V,d)`, `|V|=n`. Greedy edge-selection builds an edge set `F ⊆ E` by scanning `E` in nondecreasing cost and adding `e` whenever `F ∪ {e}` remains a **subset of some Hamiltonian cycle** — i.e. `F ∪ {e}` is a *linear forest* (vertex-disjoint simple paths): max degree ≤ 2 and acyclic, with the unique exception of the closing `n`-th edge. The accept test factors into:
- a **degree constraint** `deg_F(u), deg_F(v) ≤ 1` before adding, and
- a **no-premature-cycle constraint** `u, v` in different path-components (Union–Find), lifted only for the final edge.

## 2. Why greedy is optimal for MST but not for TSP: matroids

**Kruskal's algorithm** is the same greedy, and it is *optimal* because the acyclic edge sets of a graph form the independent sets of the **graphic matroid** `M`. The greedy algorithm maximizes/minimizes a linear objective exactly over the independent sets of a matroid (Rado–Edmonds theorem) — this is the precise reason "sort and add if feasible" works for MST.

For TSP, feasibility = "extendable to a Hamiltonian cycle" = linear forest. Linear forests are the **intersection of two matroids**:
- the graphic matroid (acyclicity), and
- the **partition matroid** capping each vertex's degree at 2 (a "degree-bounded" matroid).

Greedy is optimal over **one** matroid but **not** over a **matroid intersection** in general. Even worse, the Hamiltonian-cycle constraint is *stronger* than 2-matchings (it forbids short subtours globally), placing exact TSP outside polynomial matroid-intersection methods entirely (it's NP-hard). So cheapest-link is "greedy applied where the matroid theory no longer guarantees optimality" — the structural reason it joins nearest neighbor in having only a `Θ(log n)` ratio. (A degree-≤2 acyclic edge set *is* computable optimally as a **minimum-weight 2-matching / matching-based relaxation**, but that yields disjoint subtours, not a single tour — the gap cheapest-link papers over greedily.)

## 3. Worst-case analysis

**Theorem (Rosenkrantz–Stearns–Lewis 1977).** The greedy (cheapest-link) heuristic for metric TSP has worst-case ratio `Θ(log n)`: `GREEDY(I)/OPT(I) ≤ ½(⌈log₂ n⌉ + 1)` with matching `Ω(log n)` instances — the same asymptotic bound as nearest neighbor, proved by analogous charging/dyadic arguments. The intuition: forced to skip locally-cheap edges (blocked by degree-2 or premature-cycle constraints), the algorithm defers connections into late, costly "bridge" edges between fragments, and adversarial metrics stack these into a logarithmic blow-up.

**Non-metric:** no bounded ratio (inherits TSP inapproximability).

## 4. Annotated pseudocode

```
CHEAPEST_LINK(V, d):
    E ← sort edges by nondecreasing d                 # Θ(n² log n) dominant
    deg ← 0^n ;  uf ← MakeSet(V) ;  F ← ∅
    for (u,v) in E:
        if deg[u] ≤ 1 and deg[v] ≤ 1 and Find(uf,u) ≠ Find(uf,v):   # linear-forest invariant
            F ← F ∪ {(u,v)}; deg[u]++; deg[v]++; Union(uf,u,v)
            if |F| == n−1: break                       # a Hamiltonian path remains
    (a,b) ← the two vertices with deg == 1             # unique endpoints
    F ← F ∪ {(a,b)}                                    # close to a Hamiltonian cycle
    return F
```

```mermaid
flowchart LR
    A["sorted edge stream (cheapest first)"] --> B{"deg(u)≤1 ∧ deg(v)≤1<br/>∧ different components?"}
    B -- accept --> C["add edge; union; deg++"]
    B -- reject --> A
    C -->|"|F| < n−1"| A
    C -->|"|F| = n−1"| D["close: join the two deg-1 endpoints"]
    D --> E([Hamiltonian tour])
    classDef m fill:#eee; class A,B,C,D m
```

The control structure is Kruskal with two extra rejections (degree cap, and final-edge special-casing) — the **two-matroid** nature made operational.

## 5. Edge cases & invariants

- **Invariant:** after each acceptance `F` is a linear forest (∀v: `deg ≤ 2`; acyclic) ⇒ always completable to a tour; at `|F| = n−1` it is a single Hamiltonian path with exactly two degree-1 endpoints.
- **Non-metric `d`:** the `Θ(log n)` bound requires the triangle inequality; without it the ratio is unbounded.
- **Incomplete graphs:** greedy can **deadlock** — reach a state where the two remaining endpoints are non-adjacent or every legal edge is blocked — failing to produce a tour. Remedy: run on the metric closure (all-pairs shortest paths). On a complete graph deadlock cannot occur (the final endpoints are always adjacent).
- **Ties:** broken arbitrarily; affects the output tour but not validity. Lexicographic/stable tie-breaking aids reproducibility.
- **Symmetry:** assumes symmetric `d`; the degree-2 linear-forest invariant is for undirected tours (ATSP needs in/out-degree bookkeeping and is a different construction).

## 6. Optimizations & practical notes

- **Candidate lists:** instead of sorting all `Θ(n²)` edges, restrict to each vertex's `k`-nearest neighbors (`k ≈ 5–10`); sort only those `O(nk)` edges. Standard in fast TSP codes; drops construction to `O(nk log(nk))` with negligible quality loss, occasionally needing a repair pass if candidate edges are exhausted before the tour closes.
- **Union–Find with path-endpoint tracking:** maintain, per path-component, its two endpoints, so the "would this close a premature cycle?" and "what are the final two endpoints?" queries are `O(α(n))`.
- **Savings algorithm (Clarke–Wright) kinship:** for VRP, the classic *savings* heuristic is a cheapest-link-style edge-merging greedy on a savings metric — same "merge cheapest compatible fragments" paradigm.
- **As a seed:** like nearest neighbor, feed the result to **2-opt / Or-opt / Lin–Kernighan**. Cheapest-link tours are often a touch better seeds than NN (no single bad "closing edge"; instead several moderate bridge edges), and local search removes the remaining crossings.
- **Empirical:** ~10–15% over optimal on random Euclidean instances, typically edging out single-start nearest neighbor; both are dominated by insertion heuristics (constant ratio) on the worst case.

**Synthesis:** greedy edge-selection is Kruskal's MST greedy retargeted at tours — sort edges, add the cheapest that preserves the **linear-forest** invariant (degree ≤ 2, no premature cycle), close at the end. The feasibility structure is a **matroid intersection**, not a single matroid, which is exactly why the Rado–Edmonds optimality of greedy evaporates and the heuristic inherits the `Θ(log n)` worst-case ratio (and unbounded non-metric ratio). It is a fast, `Θ(n² log n)` constructor — globally bargain-driven rather than position-driven like nearest neighbor — best used as a local-search seed, and a clean illustration of *where matroid theory stops guaranteeing greedy optimality.*
