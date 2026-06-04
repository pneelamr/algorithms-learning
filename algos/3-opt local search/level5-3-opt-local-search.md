# 3-opt local search — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; better than 2-opt, slower
> **Used for:** Improving a tour via 3-edge reconnections for higher quality than 2-opt
> **Level 5 of 6** — graduate: the reconnection group, the k-opt ladder, Or-opt, complexity, the double-bridge gap. See sibling files for other levels.

---

## 1. The 3-exchange neighborhood, precisely

Fix three deleted edges at tour positions `i<j<k`, giving ordered segments `A,B,C` (each a directed path). A reconnection chooses, for each segment, an **orientation** (forward/reversed) and arranges the three into a single cycle. Of the `2³ · (cyclic arrangements)` formal combinations that yield a valid Hamiltonian cycle, **8** are distinct as tours; removing the identity leaves the **7** non-trivial 3-opt moves. Up to relabeling they are:

- **`2-opt-type` (3 moves):** reverse one segment — `A'BC`, `AB'C`, `ABC'`. Each equals a single 2-exchange on one of the three deleted edges' neighborhoods.
- **`double-2-opt` (3 moves):** reverse two segments — `A'B'C`, `A'BC'`, `AB'C'`. Each is a composition of two 2-exchanges.
- **`pure 3-opt` (1 move):** `ACB` (or equivalently a non-adjacent **segment relocation**) with **no reversal** — the unique element of `N₃ \ N₂`-closure that genuinely *moves* a block. This is the **Or-opt** move when the relocated block is short.

So `N₂ ⊆ N₃` as move sets, giving **`3-opt local optima ⊆ 2-opt local optima`** — the formal monotonicity of the k-opt ladder.

## 2. The k-opt ladder and diminishing returns

For general `k`, the `k`-exchange neighborhood has size `Θ(n^k)` and `k`-optimal ⊆ `(k−1)`-optimal. Empirically on random Euclidean instances:

```
local optimum     neighborhood    time/pass     typical excess over OPT
  2-opt            Θ(n²)           fast          ~5%
  3-opt            Θ(n³)           ~10–30× 2-opt  ~3%
  4-opt+           Θ(n⁴)           prohibitive   ~2–3% (marginal)
  Lin–Kernighan    variable depth  ~3-opt-ish     ~1–2%
```

The lesson driving the rest of the cluster: **raising fixed `k` gives diminishing quality for exploding cost.** Lin–Kernighan abandons fixed `k` entirely — it builds a variable-length sequential exchange and lets the *gain criterion* decide depth per move, achieving `~k=5`-quality at `~3-opt` cost. 3-opt is thus best read as the *last* fixed-`k` member people use directly before switching to variable depth.

## 3. Or-opt: the practical restriction

The pure-3-opt relocation is so valuable (and the full `Θ(n³)` scan so costly) that the standard practice is **Or-opt**: relocate chains of length `L ∈ {1,2,3}` to a new position (optionally reversed). This restricts the pure-3-opt move to short blocks, dropping the neighborhood to `Θ(n²)` while capturing most of 3-opt's gain over 2-opt. Or-opt is also the **asymmetric-TSP-friendly** member: relocating a short chain needs no long-segment reversal, so its gain stays a constant-size local quantity even on ATSP (where full 2-opt/3-opt reversal is expensive).

## 4. Complexity and hardness

- **Neighborhood scan:** `Θ(n³)` per pass; with neighbor lists the *effective* candidate count is near-linear per anchor city but the constant and worst case remain above 2-opt.
- **Convergence:** like 2-opt, the number of improving steps is **exponential** in the worst case, and computing a 3-opt local optimum is **PLS-complete** (same framework as 2-opt; k-opt PLS-completeness, Krentel / Johnson–Papadimitriou–Yannakakis lineage).
- **No approximation guarantee.** 3-opt has no constant worst-case ratio (its optima are a subset of 2-opt's, so its worst case is no worse than 2-opt's `Θ(log n / log log n)` Euclidean bound, but no constant bound is known/holds in general).

## 5. The double-bridge gap (why 3-opt isn't the end)

A **double bridge** deletes four edges splitting the tour into segments `A,B,C,D` and reconnects as `A · C · B · D`. Key structural facts:
- It is a **4-opt** move, so outside `N₃` — **no** sequence-preserving 3-opt move realizes it.
- It is **non-sequential**: it cannot be built by Lin–Kernighan's sequential chain of single edge swaps either (LK's moves are sequential by construction).
- It changes the tour "globally" while keeping **four long segments intact**, so it perturbs without destroying structure.

These three properties make the double bridge the canonical **perturbation kick** for escaping 2-/3-opt and LK local optima — the foundation of **chained Lin–Kernighan** and iterated local search. 3-opt's inability to make this move is precisely the wall that motivates restart-based methods.

## 6. Edge cases & invariants

- **Symmetry.** The 2-opt-type and double-2-opt reconnections reverse segments (costly/awkward on ATSP); Or-opt avoids this and is the ATSP-safe restriction.
- **Invariant.** Every reconnection considered yields a single Hamiltonian cycle; disconnected reconnections are excluded a priori by enumerating only the 7 valid types.
- **Pivoting & seeding.** Seeding 3-opt with a 2-optimal tour (rather than a raw constructed tour) is standard: 3-opt then only does the expensive work where the cheaper move is exhausted.
- **Ties / ε-tolerance** as in 2-opt.

## 7. Synthesis

3-opt is iterative improvement over the `Θ(n³)` 3-exchange neighborhood, whose 7 reconnections add **segment relocation** to 2-opt's reversal — strictly strengthening the local optima (`3-opt ⊆ 2-opt`) at an order-of-magnitude cost. It is the apex of *practical fixed-`k`* local search and the pedagogical hinge of the cluster: its diminishing-returns curve motivates **variable-depth Lin–Kernighan**, and its inability to make the **double-bridge** 4-opt move motivates **chained Lin–Kernighan**. The same trade recurs throughout the improvement tier — more neighborhood power for better tours, never a worst-case guarantee.

