# Cheapest insertion — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Logistics route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by always making the globally cheapest insertion
> **Level 5 of 6** — graduate: the joint-selection formalism, the 2-bound, nearest-vs-cheapest, complexity, edge cases. See sibling files for other levels.

---

## 1. Formal setting and the family decomposition

Metric TSP on `(V,d)`. An insertion heuristic maintains a Hamiltonian cycle `T_S` on growing `S`, with two policies (cf. the nearest-insertion entry):
- **Selection `σ`:** which `k ∈ V\S` enters.
- **Placement (fixed):** insert at `argmin_{(i,j)∈T_S} c(i,k,j)`, `c(i,k,j)=d(i,k)+d(k,j)−d(i,j) ≥ 0`.

The conceptual point of cheapest insertion: it **collapses selection and placement into one joint minimization**. Where nearest insertion sets `σ = argmin_k dist(k,S)` and then places, cheapest insertion sets

```
(k*, position*) = argmin_{k ∉ S, (i,j) ∈ T_S} c(i,k,j),
```

i.e. its selection rule is *defined by* the placement objective. It is the family member whose greedy step is exactly "minimize the immediate tour-length increase." This makes it the most direct combinatorial-greedy of the family — and a natural baseline for greedy route construction.

## 2. The 2-approximation, via domination of nearest insertion

**Theorem (RSL 1977).** Metric cheapest insertion returns `cost(T) ≤ 2·OPT`, tight.

**Proof.** Run cheapest insertion. At the step that adds the `|S|`→`|S|+1` vertex, let `Δ_chp` be the cost paid. Consider the *alternative* insertion nearest insertion's analysis uses on the **same sub-tour `S`**: take `v = argmin_{k∉S} dist(k,S)` and insert it adjacent to its nearest tour-vertex; that alternative costs `Δ_alt ≤ 2·dist(v,S)` (triangle inequality, as in the nearest-insertion proof). Since cheapest insertion takes the **global minimum** insertion over *all* `(k, position)`, `Δ_chp ≤ Δ_alt ≤ 2·dist(v_S, S)` where `v_S` is the tour-nearest vertex at that sub-tour.

Now sum over the `n−2` insertions. The sequence of values `dist(v_S, S)` — the connection distances of the *tour-nearest* vertex as `S` grows — sums to `cost(MST)` by the **Prim correspondence** (Prim adds the tree-nearest vertex; the connection distances are the MST edge weights). Hence

```
cost(T) = Σ Δ_chp ≤ Σ 2·dist(v_S, S) = 2·cost(MST) ≤ 2·OPT.   ∎
```

The elegant part: we never analyze cheapest insertion's *actual* choices — only that each is **dominated** by a nearest-style insertion whose costs telescope to `2·MST`. **Tightness** matches nearest insertion: families of instances drive `cost/OPT → 2`.

## 3. Nearest vs. cheapest: same bound, different mechanism

Both are tight 2-approximations, but they are **not** the same algorithm:
- **Nearest** uses a *local* selection criterion (`dist(k,S)`) and is structurally Prim-faithful — its connection distances *are* the MST.
- **Cheapest** uses a *global* criterion (joint min `c`), so it may insert a vertex that is **not** the tour-nearest if a cheap gap exists elsewhere (as on the shared 4-city instance, where cheapest inserts D before C while nearest inserts C before D). It is *not* Prim-faithful; the MST only appears as an **upper bound** via domination, not as an identity.
- Empirically they are comparable (~10–25% over OPT on random Euclidean), with cheapest sometimes marginally better at a larger per-step constant. Neither beats **farthest insertion** in practice — the family's worst-case/average-case inversion (next sibling).

So cheapest insertion is the case where the *greedy-looking* rule and the *provable* rule coincide in **bound** but diverge in **trajectory** — a clean illustration that the 2-bound is a property of the *insertion paradigm + metric*, not of any one selection rule.

## 4. Complexity and data structures

- **Joint argmin, cached:** maintain per outside vertex `k` its best insertion cost `cost[k]` and position `pos[k]`. Each insertion replaces edge `(i*,j*)` by `(i*,k*),(k*,j*)`; for each `k∉S` re-evaluate `c` against the **two new edges** (and discard `pos` on the removed edge) in `O(1)`, plus consider insertion *adjacent to* `k*`. Total `O(n²)` time, `Θ(n²)` space.
- **Argmin** per step is `O(n)` (linear scan) or `O(log n)` with a priority queue keyed by `cost[k]` (with lazy decrease-key on update).
- The constant exceeds nearest insertion's because we track a best **position** (an edge), not just a scalar distance. Still `Θ(n²)`.

## 5. Edge cases & invariants

- **Metric required.** `c ≥ 0` and the domination step both need the triangle inequality; non-metric inputs void the bound and can make insertions reduce length, breaking the analysis.
- **Invariant.** `T_S` is always a Hamiltonian cycle; `cost[k]/pos[k]` are the exact cheapest insertion of `k` into the current `T_S`.
- **Initialization.** Any seed preserves the bound; closest-pair conventional. Seed perturbs the trajectory, not the guarantee.
- **Ties** (equal cheapest insertions) broken arbitrarily — explains nearest-vs-cheapest order differences under different tie rules.
- **ATSP.** Cheapest-insertion cost and the MST/Prim argument assume symmetry; directed variants lose the clean 2-bound.

## 6. Synthesis

Cheapest insertion is the insertion paradigm with selection **fused into** the cheapest-placement objective: each step makes the globally minimal-increase insertion. Its metric **2-approximation** is proved not from its own trajectory but by **domination** — every cheapest step costs no more than a nearest-style step, whose connection distances telescope (Prim) to `2·MST ≤ 2·OPT`. It shares nearest insertion's `Θ(n²)` cost (larger constant) and ≤ 2 guarantee while following a different, globally-greedy path, and — like its sibling — is empirically beaten by **farthest insertion**, the upcoming inversion. It is the family's most natural greedy baseline and a standard template for logistics route construction.

