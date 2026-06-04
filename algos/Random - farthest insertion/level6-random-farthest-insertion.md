# Random / farthest insertion — Level 6: Researcher / frontier

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Multi-start metaheuristic seeding  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound
> **Used for:** Generating diverse starting tours by inserting far/random nodes early
> **Level 6 of 6** — researcher: the inversion, random-insertion analysis frontier, variants, open problems, references. See sibling files for other levels.

---

## 1. Position: the family's empirical winners, proven-bound laggards

Farthest and random insertion close the constructive insertion family and crystallize its central research lesson: **the worst-case ranking inverts the practical ranking.** Nearest and cheapest insertion are tight metric 2-approximations; farthest and random hold only the generic `O(log n)` insertion bound — yet farthest is, across decades of experiments, the **best simple constructor** in the family, and random is close behind while supplying decorrelated seeds. The pair's modern relevance is almost entirely as **seed generators** for local search (2-opt, Lin–Kernighan, chained LK) and **multi-start** metaheuristics.

```
Constructive heuristic   proven metric worst case        empirical (rand. Euclidean)   role
  Nearest insertion      ≤ 2 (tight, Prim-faithful)       ~20%                          baseline
  Cheapest insertion     ≤ 2 (tight, by domination)       ~15–20%                        greedy baseline
  Farthest insertion     only generic O(log n)            ~10–15% (best of family)       strong single seed
  Random insertion       generic O(log n) (expected)      ~10–15%                        seed ensembles
  Christofides–Serdyukov 3/2                              ~10%                           approx. benchmark
```

## 2. The inversion, sharpened

RSL 1977 prove (i) nearest/cheapest ≤ 2, (ii) a **generic** bound `⌈log₂ n⌉ + 1` for *any* cheapest-placement insertion rule, which is all that is provably inherited by farthest and random. No constant-factor approximation is known for farthest insertion; constructing instances that force its ratio above any fixed constant vs. proving a constant upper bound is a long-standing gap. The **skeleton-first** mechanism (far/diameter vertices placed while the tour is flexible → near-convex outline → cheap interior refinement) is the accepted *explanation* for farthest's empirical dominance, but a rigorous average-case account remains incomplete — the same open thread flagged across the family.

## 3. Random insertion: the analysis frontier

Random insertion is the family member with the most active rigorous study:
- Its worst case is the generic `O(log n)`; its **average-case** quality on random Euclidean and on general metrics is markedly better, and tightening these bounds is ongoing.
- A line of work analyzes random insertion's competitive ratio and its expected approximation on structured inputs (convex position, random metrics); random insertion is repeatedly singled out — alongside farthest — for performance disproportionate to its simplicity.
- The **diversity** of its output distribution is itself an object of study for multi-start theory: how seed variance maps to post-local-search basin coverage.

(Specific constants here are model-dependent and partly open; treat numeric claims as empirical/representative — see flags.)

## 4. Variants and applications

- **Convex-hull insertion** (Stewart 1977; Golden et al. 1980): seed with the convex hull — an explicit "skeleton-first" construction that formalizes farthest insertion's implicit outline-building; then cheapest/largest-angle insert interior points. Among the strongest simple Euclidean constructors.
- **Greatest-angle / max-regret insertion:** geometric and regret-based selection rules in the same placement framework.
- **Multi-start & GRASP:** random insertion is a natural **greedy-randomized construction** step; GRASP = randomized construction + local search + repeat, exactly the "diverse seeds → polish → keep best" pattern.
- **Restart engines:** random insertion seeds for chained Lin–Kernighan and other iterated local searches; farthest insertion as the deterministic warm start.
- **VRP:** farthest/insertion seeds in route construction; parallel and regret insertion descendants (Solomon I1, Clarke–Wright) in vehicle routing.

## 5. Open problems / threads

1. **Constant-factor bound for farthest insertion** — prove one or rule it out; the headline open question of the family.
2. **Tight average-case constants** for farthest and random on random Euclidean / random metric inputs.
3. **Rigorous skeleton-first theory:** formalize why diameter-first insertion beats nearest on typical inputs (convex-outline / subadditivity arguments).
4. **Seed-variance → solution-quality** maps for multi-start: how construction diversity translates to post-LK quality and how to optimize the seed distribution.
5. **Learned selection policies** interpolating nearest/cheapest/farthest/random, with cross-distribution guarantees.

## 6. Links to related problems

- **Insertion-family siblings (this registry):** Nearest insertion and Cheapest insertion (tight ≤ 2; the proven bounds farthest/random *fail* to match). Christofides–Serdyukov (3/2) is the next guarantee tier.
- **Shared 2-bound cousin:** MST doubling (2 via the MST); the **Prim correspondence** that gives nearest its tight 2 is exactly what farthest's `argmax` forgoes.
- **Greedy contrast:** nearest neighbor & greedy-edge (`Θ(log n)`).
- **Forward — improvement & metaheuristics:** these are *seeds* for 2-opt, 3-opt, Lin–Kernighan, chained Lin–Kernighan; random insertion feeds **GRASP**, multi-start, and restart loops — bridging to the metaheuristics cluster (simulated annealing, tabu search, GRASP, VNS, genetic / ant-colony).
- **Paradigm & tooling:** convex hull, Prim's MST, subadditive Euclidean functionals (BHH), Solomon / Clarke–Wright insertion for VRP.

## References (seminal → modern)

- Rosenkrantz, Stearns & Lewis (1977). *An analysis of several heuristics for the traveling salesman problem.* (Nearest/cheapest ≤ 2; the generic `⌈log₂ n⌉+1` insertion bound covering farthest/random.)
- Johnson & McGeoch (1997). *The TSP: a case study in local optimization.* (Empirical family comparison; farthest best; seed quality for local search.)
- Stewart, W. (1977); Golden, Bodin, Doyle & Stewart (1980). *Convex-hull and insertion constructions.*
- Beardwood, Halton & Hammersley (1959). *The shortest path through many points.* (Euclidean `β√n` law for average-case framing.)
- Feo, T. & Resende, M. (1995). *Greedy randomized adaptive search procedures (GRASP).* (Randomized construction + local search; the multi-start frame for random insertion.)
- (Modern) analyses of the **random insertion** heuristic for TSP (average-case / competitive bounds, 2010s).

*Uncertainty flags:* the "farthest is best in practice" and ~10–15% figures are empirical and distribution-dependent (Johnson–McGeoch). Farthest insertion has **no known constant-factor guarantee**; only the generic `O(log n)` insertion bound is proven. Random-insertion average-case constants are model-dependent and partly open. The shared 4-city worked instance is used for cross-artifact comparison and is not strictly metric; the stated guarantees hold for genuine metric inputs.

