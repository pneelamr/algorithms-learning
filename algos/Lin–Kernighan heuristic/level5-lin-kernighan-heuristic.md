# Lin–Kernighan heuristic — Level 5: Graduate

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; near-optimal in practice
> **Used for:** Variable-depth edge swaps producing near-optimal tours
> **Level 5 of 6** — graduate: the sequential-exchange formalism, the gain lemma, α-nearness, complexity, LKH. See sibling files for other levels.

---

## 1. Formal model: λ-opt by sequential exchange

LK searches a subset of the **`λ`-opt** neighborhood (all `k`-exchanges, `k` unbounded) but only its **sequential** elements — those expressible as an alternating trail. A move is a sequence of edges
`x_1, y_1, x_2, y_2, …, x_k, y_k` with `x_i ∈ T` (removed), `y_i ∉ T` (added), forming a closed alternating trail on vertices `t_1,…,t_{2k}` with `y_k = (t_{2k}, t_1)` closing the tour. Restricting to **sequential** moves is the key approximation: it makes the neighborhood searchable greedily, at the cost of excluding **non-sequential** `λ`-opt moves — most importantly the **double bridge** (see §5), which no sequential LK move realizes.

## 2. The gain criterion and its justification

Per-step gain `g_i = d(x_i) − d(y_i)`; partial sum `G_i = Σ_{j≤i} g_j`. LK extends only while `G_i > 0` and chooses each `y_i` to maximize `g_i` over candidate edges.

**Lemma (Lin–Kernighan, "positive gain").** *If real numbers `a_1,…,a_m` satisfy `Σ a_j > 0`, then there is a cyclic rotation `a_{p}, a_{p+1}, …` all of whose partial sums are positive.*

Consequence: any sequential exchange with **overall** positive gain has an equivalent ordering with **every** prefix positive. So the greedy "keep `G_i > 0`" rule **never excludes an improving sequential move** — it only bounds the search. This is the theoretical license for LK's greedy depth control and the reason variable depth is both safe and efficient.

## 3. Feasibility / closeability

At each depth the trail must be **closeable**: adding `y_i* = (t_{2i}, t_1)` and removing the in-progress `x_{i+1}` must yield a single Hamiltonian cycle, not a multi-subtour 2-factor. LK enforces this by the alternation rules (`x`'s on the tour, `y`'s off; `t_{2i+1}` chosen so the forced `x_{i+1} = (t_{2i+1}, t_{2i+2})` keeps closure possible) and by disallowing re-adding removed edges. The original LK additionally uses **limited backtracking** at the first two levels (breadth ≈ 5 for `y_1`, `y_2`), which empirically supplies most of the quality; deeper levels are pure greedy.

## 4. Candidate lists and α-nearness (the LKH leap)

Adds `y_i` are restricted to a **candidate list** per vertex — without it the branching factor is `n`. Quality hinges on candidate quality:
- **k-nearest neighbors:** the classic choice (≈5–10 nearest).
- **α-nearness (Helsgaun, LKH):** rank edges by `α(i,j) = L(T⁺(i,j)) − L(T)`, the increase in the **Held–Karp 1-tree** length when edge `(i,j)` is forced in. This is a *far* better predictor of "appears in good/optimal tours" than Euclidean nearness, because the 1-tree is the Lagrangian/Held–Karp **lower bound** structure from the exact-methods cluster. α-nearness is the single biggest reason **LKH** outperforms classic LK — a direct reuse of the Held–Karp 1-tree bound (see the Held–Karp and branch-and-bound entries) inside a heuristic.

## 5. The non-sequential gap → why LK is not the end

LK explores **sequential** `λ`-opt moves only. The **double bridge** — delete 4 edges splitting the tour into `A,B,C,D`, reconnect as `A·C·B·D` — is a 4-opt move that is **not** a sequential exchange: its alternating trail does not close as a single chain. Therefore:
- LK (and 2-/3-opt) **cannot reach it** in one move.
- It keeps four long segments intact, so it perturbs **structure-preservingly**.

These make the double bridge the canonical **kick** to escape LK local optima — the basis of **chained / iterated LK** (final entry). Thus LK's defining approximation (sequential-only) is exactly the wall the next method climbs.

## 6. Complexity

- **Per LK move:** `O(breadth^2 · depth)` candidate evaluations + closure cost. With α-nearness candidate lists (size ~5) and don't-look bits, the dominant practical cost is **segment reversal**, handled in `O(√n)` (two-level list) or `O(log n)` (splay/2-level tree) — the same machinery as 2-opt.
- **Full local optimization:** empirically `≈ O(n^{2.2})` (Johnson–McGeoch) on geometric instances.
- **Convergence/hardness:** as a local search over the (sequential) `λ`-opt neighborhood, finding an LK local optimum inherits the **PLS-hard** character of k-opt; worst-case step counts are not polynomially bounded. No approximation guarantee.

## 7. Edge cases & practice

- **Symmetric only** in classic form (reversals); ATSP needs directed variants and loses the clean sequential structure.
- **Depth limit & breadth schedule** trade speed vs quality; LKH exposes these plus **5-opt sequential submoves** as the base move.
- **Seeding:** LK is typically seeded by a fast constructor (greedy-edge / space-filling-curve / nearest neighbor) and often a 2-opt pre-pass; the seed barely affects final quality but affects time.
- **Determinism:** classic LK is deterministic per start; randomized candidate tie-breaking feeds the restart loop of chained LK.

## 8. Synthesis

LK is iterative improvement over the **sequential `λ`-opt** neighborhood, with depth chosen per move by the **positive-gain criterion** (justified by the LK lemma) and quality driven by **α-nearness candidate lists** rooted in the **Held–Karp 1-tree** bound. It achieves ~1–2% above optimal — the best single-trajectory local search — by adapting depth to the instance. Its one structural blind spot, the **non-sequential double bridge**, is precisely what **chained Lin–Kernighan** exploits as a perturbation, and its candidate-list machinery (α-nearness) is the bridge tying this heuristic back to the exact lower-bound theory of the earlier cluster.

