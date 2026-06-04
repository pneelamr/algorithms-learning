# Christofides–Serdyukov — Level 6: Researcher / frontier

> **Example problems:** Metric Traveling Salesman Problem, Vehicle Routing Problem, Logistics / delivery route planning  ·  **Type:** Approximation  ·  **Guarantee:** At most 1.5× optimal (metric TSP)
> **Used for:** Balanced quality/speed approximation with a worst-case bound
> **Level 6 of 6** — researcher: the 45-year record, the 2021 breakthrough, variants, hardness, open problems, references. See sibling files for other levels.

---

## 1. Position: the 45-year high-water mark

Christofides–Serdyukov (1976/1978) gave the **3/2-approximation** for metric TSP and remained, astonishingly, the **best known approximation ratio for 45 years** — one of the most famous standing records in algorithms. It is the culmination of the classical combinatorial approach (MST + matching + Euler + shortcut) before the field needed LP relaxations and randomization to inch further.

```
Metric TSP approximation timeline
  MST doubling                       2          (folklore / Rosenkrantz–Stearns–Lewis 1977)
  Christofides–Serdyukov  1976/78    3/2        ← record holder 1976–2021
  Karlin–Klein–Oveis Gharan  2021    3/2 − ε    ε ≈ 10^-36, randomized — first improvement ever
  Euclidean / planar (Arora, Mitchell)  PTAS    (1+ε), special metrics
```

## 2. The 2021 breakthrough and why 3/2 was so hard to beat

**Karlin, Klein & Oveis Gharan (STOC 2021)** gave the first metric-TSP approximation below 3/2: `3/2 − ε` for a tiny but positive `ε ≈ 10^{-36}`. The method abandons the deterministic MST and instead:
- Samples a spanning tree from the **maximum-entropy distribution** over spanning trees defined by the optimal **Held–Karp LP** solution (a strongly-Rayleigh / determinantal measure), then
- performs a parity correction whose *expected* cost beats Christofides' matching by a sliver, using the strong negative-dependence properties of the random tree.

Why 3/2 resisted for decades: Christofides' two bounds (`tree ≤ OPT`, `matching ≤ OPT/2`) are each individually tight, and they are tight on *different* instances; beating 3/2 requires exploiting that they cannot be *simultaneously* tight, which needs the global view of the Held–Karp LP and randomized rounding rather than the MST. The improvement is currently of purely theoretical interest (the `ε` is astronomically small).

## 3. Variants and special cases

- **Path TSP (s–t Hamiltonian path):** a Christofides analogue gives 5/3 (Hoogeveen 1991); improved to 3/2 (Zenklusen 2019) and then matched to the cycle case — the "path = cycle" question for TSP approximation, now essentially resolved.
- **Graph TSP** (shortest-path metric of an unweighted graph): below 3/2 well before KKG — Oveis Gharan–Saberi–Singh, Mömke–Svensson, Mucha (13/9), **Sebő–Vygen 7/5** (2014).
- **Euclidean / planar / bounded-doubling / bounded-genus:** admit **PTASs** (Arora 1998; Mitchell 1999) — `(1+ε)` for any `ε`, dominating Christofides where applicable.
- **A deterministic Christofides on the LP tree:** various "best-of-both" and LP-rounding analyses (e.g. the "Christofides–Serdyukov on a fractional tree") give refined bounds and are pedagogically central to the KKG line.

## 4. Lower bounds, integrality gap, hardness

- **APX-hardness:** metric TSP has no PTAS unless P=NP; explicit inapproximability `123/122` (Karpinski–Lampis–Schmied 2015), graph-TSP `185/184`. So a constant is the best *form* of result; the open contest is the precise constant between ~`1.0082` (hardness) and `3/2 − ε` (algorithm).
- **Held–Karp integrality gap:** the subtour-elimination LP (same bound discussed in the Held–Karp DP and Branch-and-cut siblings) has a gap **conjectured to be 4/3** (Goemans), with `3/2` long the best proven upper bound (KKG nudged it below). Christofides' analysis can be recast against the LP, and the `4/3` conjecture is the natural target — proving it would likely come with a `4/3`-approximation.
- The gap between the `4/3` conjecture and the `3/2 − 10^{-36}` algorithm is the headline quantitative open problem of metric-TSP approximation.

## 5. Open problems

1. **A practical sub-3/2 algorithm** — KKG's `ε` is negligible; a clean, sizeable improvement (ideally toward `4/3`) is open.
2. **Deterministic** sub-3/2 metric TSP (KKG is randomized).
3. **Prove (or refute) the 4/3 integrality-gap conjecture** for the Held–Karp LP.
4. **Tighten path-TSP / ATSP / graph-TSP** constants and unify the techniques.
5. **Faster matching:** Christofides' `O(n³)` is matching-bound; near-linear-time `(3/2+ε)`-approximate-matching variants and their effect on the guarantee.

Christofides itself is settled (ratio 3/2 tight, `O(n³)`); its frontier role is as the **benchmark to beat** and the structural template (parity correction quality) that every improvement reinterprets.

## 6. Links to related problems

- **Direct sibling / predecessor (this registry):** MST doubling (same skeleton, ratio 2; Christofides upgrades the parity fix from doubling to matching).
- **Ingredients:** minimum spanning tree (Prim/Kruskal), **minimum-weight perfect matching** (Edmonds' blossom), Eulerian circuits / `T`-joins, Held–Karp / subtour LP (shared lower bound with Held–Karp DP & Branch-and-cut).
- **Improvement partners:** 2-opt / Or-opt / Lin–Kernighan local search consume the 3/2 tour as a high-quality seed.
- **Paradigm:** approximation algorithms, LP relaxation & randomized rounding, max-entropy/strongly-Rayleigh distributions (KKG), PTAS for geometric TSP, integrality-gap analysis.

## References (seminal → modern)

- Christofides, N. (1976). *Worst-case analysis of a new heuristic for the travelling salesman problem.* CMU report. — and Serdyukov, A. (1978), independent USSR discovery.
- Rosenkrantz, Stearns & Lewis (1977). *An analysis of several heuristics for the TSP.* (Doubling baseline.)
- Held, M. & Karp, R. (1970). *The traveling-salesman problem and minimum spanning trees.* (Subtour LP / 4/3-gap context.)
- Hoogeveen, J. (1991). *Analysis of Christofides' heuristic for the path TSP* (5/3).
- Goemans, M. (1995). *Worst-case comparison of valid inequalities for the TSP* (integrality-gap conjecture).
- Sebő, A. & Vygen, J. (2014). *Shorter tours by nicer ears* (graph-TSP 7/5).
- Karpinski, Lampis & Schmied (2015). *New inapproximability bounds for TSP.*
- Zenklusen, R. (2019). *A 1.5-approximation for path TSP.*
- Karlin, A., Klein, N. & Oveis Gharan, S. (2021). *A (slightly) improved approximation algorithm for metric TSP.* STOC. (First sub-3/2.)

*Uncertainty flags:* KKG's improvement is rigorous but the `ε ≈ 10^{-36}` has no practical impact — Christofides remains the practical gold standard. The `4/3` integrality-gap conjecture is **open**. Inapproximability constants are the best explicit known, not tight. Christofides' own bounds (3/2 ratio, tightness, `O(n³)`) are fully settled.
