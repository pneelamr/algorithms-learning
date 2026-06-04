# Chained Lin–Kernighan — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Large-scale Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; often extremely close to optimal
> **Used for:** Restarting Lin–Kernighan from perturbed tours for large-scale, very-high-quality solving
> **Level 5 of 6** — graduate: the ILS framework, double-bridge theory, acceptance walks, scaling, the metaheuristic bridge. See sibling files for other levels.

---

## 1. Iterated local search, formally

ILS searches the space of **local optima** of a base local search `LS` rather than the full solution space. Let `s* = LS(s)` map a tour to its local optimum. ILS performs a walk on the set `S* = {LS(s)}` via:

```
s*_{t+1} = LS( Perturb(s*_t) ),    accept by  A(s*_t, s*_{t+1}, history)
```

The walk's effectiveness hinges on the **perturbation** being (i) strong enough that `LS` does not return to `s*_t` (escape), yet (ii) weak enough that `LS(Perturb(s*_t))` stays in a *nearby, correlated* basin (exploit). Chained LK instantiates `LS = LK` and `Perturb = double bridge` — the configuration empirically near-optimal for TSP's "big valley" landscape, in which good local optima cluster near the global optimum and short perturbations preserve shared backbone edges.

## 2. The double bridge: why this 4-opt move specifically

Represent the tour as a cyclic sequence; a double bridge picks cut points partitioning it into `A,B,C,D` and forms `A·C·B·D`. Properties:

- **Non-sequentiality.** The double bridge is a 4-opt move whose edge exchange is **not a closed alternating trail**, hence not expressible as a single Lin–Kernighan sequential exchange (nor any 2-/3-opt move). Formally it is the *minimal* perturbation lying outside LK's neighborhood — exactly why it escapes LK local optima rather than being undone.
- **Backbone preservation.** Only 4 edges change; the four segments (and all their internal edges) survive. In the big-valley picture this keeps `s*_{t+1}` strongly correlated with `s*_t`, so LK re-optimization is cheap and the search drifts along the valley toward the global optimum.
- **Bias-free locality.** Random cut points give an unbiased small kick; "**segment-length-bounded**" or "**local**" double bridges (cuts within a window) further reduce disturbance for very large `n`, trading exploration for cheaper re-optimization.

This is the canonical illustration that **perturbation design = neighborhood escape**: the right kick is precisely the move your local search *cannot* make.

## 3. Acceptance criteria as a random walk on `S*`

`A(·)` controls the exploration/exploitation balance on the local-optima graph:

| Criterion | Rule | Behavior |
|-----------|------|----------|
| **Better (descent)** | accept iff `cost↓` | aggressive; can stagnate in a basin of `S*` |
| **Random walk** | always accept | diffuse; loses the best without elitism |
| **Restart / better-with-restart** | accept if `cost ≤ cost(best)+ε`, else revert to `best` | the classic chained-LK choice; bounded drift |
| **Markov (Metropolis)** | accept worse w.p. `e^{−Δ/T}` | borrows simulated annealing's idea at the `S*` level |

The original **large-step Markov chain** view (Martin, Otto & Felten 1991) frames chained LK as a Markov chain on local optima with a Metropolis acceptance over "kick + LK" steps — making explicit the link to **simulated annealing**, but at the macro scale of local optima rather than individual moves.

## 4. Scaling: warm restarts and don't-look bits

The reason chained LK runs millions of iterations on `10⁶`+ cities: after a double bridge, only the **8 endpoints** of the broken/added edges are "dirty." Resetting **don't-look bits** for just those vertices (and their candidate neighbors) confines the follow-up LK to the perturbed region, so each iteration costs ≈ a *local* LK repair, not a full descent. Combined with `O(√n)`/`O(log n)` two-level tour structures (FJMO 1995) for the segment splice, the per-iteration cost is sublinear-amortized in practice. **POPMUSIC**/partition initialization and run-merging (LKH) extend this to tens of millions of cities.

## 5. Quality, guarantees, and the lower-bound coupling

- **No worst-case guarantee.** Like all of this cluster, chained LK carries no approximation bound.
- **Empirics.** Chained LK / LKH reach **< 0.1%** over optimal routinely and **find the optimum** on most TSPLIB instances; on huge geometric instances they sit within a tiny fraction of the **Held–Karp lower bound**.
- **Coupling to exact methods.** Because the **Held–Karp 1-tree bound** (Held–Karp / branch-and-bound entries) is typically ~0.7% from OPT, a chained-LK tour plus the Held–Karp bound *sandwiches* the optimum tightly — which is exactly how **Concorde** uses chained-LK upper bounds to prune its **branch-and-cut** search. The heuristic and exact clusters close the loop here.

## 6. Edge cases & practice

- **Symmetric** in standard form (LK + reversal); ATSP/constrained variants use directed LK and constraint-aware kicks (LKH-3).
- **Perturbation strength tuning:** global vs windowed double bridges; multiple kicks per iteration for rugged landscapes.
- **Acceptance tuning:** better-with-restart is robust; pure descent risks stagnation; Metropolis needs a temperature schedule.
- **Determinism/seeding:** randomized cut points (and candidate tie-breaks) make runs stochastic; the construction seed barely affects asymptotic quality, only warm-up time.

## 7. Synthesis

Chained Lin–Kernighan is **iterated local search** with LK as the engine and the **double bridge** — the minimal move outside LK's sequential neighborhood — as the perturbation, walking the graph of LK local optima under an acceptance rule, warm-started so each step is a cheap local repair. It is the capstone of the local-search cluster: it supplies the *escape* that 2-opt, 3-opt, and LK all lack, achieves near-optimal tours at extreme scale, and sandwiches the optimum together with the **Held–Karp bound** to drive exact solvers. Conceptually it is one specific answer to *"how do you escape a local optimum?"* — the question the next cluster (**simulated annealing, tabu search, genetic algorithms, GRASP, VNS**) re-answers with different perturbation/acceptance machinery.

