# Random / farthest insertion — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Multi-start metaheuristic seeding  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound
> **Used for:** Generating diverse starting tours by inserting far/random nodes early
> **Level 5 of 6** — graduate: the selection-rule formalism, the inversion, average-case theory, seeding, edge cases. See sibling files for other levels.

---

## 1. The family, and where these two sit

Metric TSP on `(V,d)`. Insertion heuristics maintain a Hamiltonian cycle `T_S` on growing `S` with a fixed **cheapest-placement** rule and a variable **selection** `σ` (see the nearest- and cheapest-insertion entries). The four canonical `σ`:

```
nearest :  σ = argmin_k dist(k,S)          provably ≤ 2 (Prim-faithful)
cheapest:  σ = argmin_k min-insertion-cost  provably ≤ 2 (dominates nearest)
farthest:  σ = argmax_k dist(k,S)          only generic O(log n) proven; best in practice
random  :  σ = uniform(V\S)                 generic O(log n) (expected); strong in practice
```

Farthest and random are grouped not by rule but by **role**: both generate strong/diverse **seed tours**. They are the family's empirical winners and its proven-bound laggards — the cleanest instance of worst-case/average-case inversion in constructive TSP heuristics.

## 2. Why farthest loses the tight 2 but wins in practice

**The lost identity.** Nearest insertion's tight `2` rests on the **Prim correspondence**: selecting the tour-nearest vertex reproduces Prim's order, so the connection distances `dist(k,S_k)` it pays are exactly MST edge weights, and `Σ Δcost ≤ Σ 2·dist(k,S_k) = 2·MST ≤ 2·OPT`. Farthest insertion selects the **argmax** connection distance — the *anti*-Prim order — so this telescoping collapses. What survives is only the **generic insertion bound**: any cheapest-placement insertion rule on a metric satisfies `cost ≤ (⌈log₂ n⌉ + 1)·OPT` (RSL 1977). Hence farthest's *proven* ceiling is `O(log n)`, strictly weaker than `2`.

**The empirical win (skeleton-first).** Define the *insertion order* as the permutation `π` in which vertices enter. Farthest insertion makes `π` place the **extreme/diameter vertices first**. Consequences:
- Early sub-tours approximate the **convex hull / global outline**; subsequent insertions are *interior* points with small `dist(k,S)`, so each `Δcost(k) ≤ 2·dist(k,S)` is tiny.
- The costly, structure-defining placements (far vertices) happen while the tour is *flexible* (few edges), not at the end when it is rigid — the opposite of nearest insertion, which strands far vertices for last and pays large closing detours.

So farthest concentrates large insertions early and small ones late, yielding lower *total* length on typical inputs despite a worse adversarial bound. This is a structural, not accidental, inversion.

## 3. Random insertion: average-case strength and diversity

Random insertion picks `σ` uniformly. Two distinct values:
- **Quality.** Empirically ~10–15% over OPT on random Euclidean — comparable to farthest, better than nearest. Its worst case is the generic `O(log n)` (in expectation), but its *average-case* behavior is much better and is the subject of the sharpest modern analyses of the family (rigorous constants remain partly open; see Level 6).
- **Diversity.** A single run is a sample from a distribution over tours. Running it `m` times yields `m` decorrelated seeds — the substrate for **multi-start** / **GRASP-style** construction and for restart-based metaheuristics. The *variance* is the feature: it spreads probability mass across distinct basins for the subsequent local search.

## 4. Seeding: the operational point

Both are used less as final answers than as **seeds**:
- **Farthest → single strong seed.** Deterministic, skeleton-first; empirically the best starting tour for 2-opt / Lin–Kernighan among simple constructors (Johnson–McGeoch report farthest seeds give better post-local-search tours than nearest/greedy seeds).
- **Random → seed ensemble.** Cheap to regenerate; feeds **chained LK's** restart loop, **GRASP** (greedy-randomized construction), and any multi-start scheme. The construction seed barely affects the *asymptotic* quality after heavy local search, but affects time-to-quality and basin coverage.

This ties the constructive family forward to the entire improvement and metaheuristic toolchain: the insertion family's last two members exist largely to feed 2-opt/3-opt/LK/chained-LK and the metaheuristics cluster.

## 5. Complexity, edge cases, invariants

- **Complexity.** `Θ(n²)` time both; random is cheapest per step (no `dist[]` maintenance needed), farthest needs the `O(n)` incremental `dist[]` update (same as nearest, with `argmax`).
- **Metric dependence.** Placement non-negativity `c ≥ 0` and the generic `O(log n)` bound need the triangle inequality. On **non-metric** inputs insertion costs can be negative (an insertion can *shorten* the tour by shortcutting an over-long edge) and all bounds void — use the metric closure if guarantees are wanted.
- **Seeding choices.** Farthest insertion is classically seeded by the **diameter pair** or **convex hull**; the seed perturbs the trajectory, not the (already weak) worst-case bound. Random insertion seeds randomly by design.
- **Invariant.** `T_S` is always a Hamiltonian cycle; `dist[k]=dist(k,S)` maintained exactly (farthest).
- **Ties / RNG.** Farthest ties broken arbitrarily; random insertion's behavior is defined relative to its RNG stream (reproducibility ⇒ fix the seed).

## 6. Synthesis

Farthest and random insertion are the insertion paradigm with **anti-Prim** (`argmax`) and **stochastic** selection. Both forfeit nearest/cheapest's tight metric `2` — retaining only the generic `O(log n)` insertion bound — yet **outperform** them empirically: farthest by laying the global skeleton first (large insertions while the tour is flexible), random by sampling diverse, well-spread build orders. They are the family's definitive **worst-case ≠ average-case** lesson and its primary **seed generators**: farthest the strongest single deterministic seed, random the engine of seed ensembles for multi-start local search (2-opt → chained LK) and the metaheuristics cluster.

