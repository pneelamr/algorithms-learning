# MST doubling (tree-doubling) — Level 6: Researcher / frontier

> **Example problems:** Metric Traveling Salesman Problem, Vehicle Routing Problem, Network / route design  ·  **Type:** Approximation  ·  **Guarantee:** At most 2× optimal (metric TSP)
> **Used for:** Fast approximate routing with a worst-case guarantee
> **Level 6 of 6** — researcher: where it sits in approximation theory, the better algorithms it seeds, hardness, open problems, references. See sibling files for other levels.

---

## 1. Position in metric-TSP approximation

MST doubling is the **entry point** of metric-TSP approximation — the simplest constant-factor algorithm, and the scaffold every stronger result modifies:

```
General TSP:  no constant-factor approx unless P=NP        (inapproximable)
Metric TSP:
   MST doubling (tree-doubling)        ratio 2        O(n²)            ← this entry
   Christofides–Serdyukov (1976)       ratio 3/2      O(n³) matching   (better parity fix)
   Karlin–Klein–Oveis Gharan (2021)    3/2 − ε        (ε ≈ 10^-36)     randomized, first sub-3/2
   Euclidean / planar / bounded-genus  PTAS           (Arora; Mitchell)
   Graph metric / unit-weight TSP      < 3/2          (Sebő–Vygen 7/5, etc.)
```

Its role today is **pedagogical and infrastructural**: the cleanest illustration of the "lower-bound-by-MST / fix parity / shortcut" paradigm, and a fast seed tour for local search. It is dominated on ratio by Christofides but is simpler and faster (no matching).

## 2. The paradigm it instantiates, and the lever for improvement

All of MST doubling, Christofides, and the recent KKG breakthrough share the skeleton: **find a connected spanning subgraph cheaper than OPT, make it Eulerian by fixing odd-degree parity, then Euler-tour + shortcut.** The only varying ingredient is the **parity correction**:
- **Doubling:** duplicate all MST edges. Cost of the fix = `M ≤ OPT` ⇒ total `≤ 2·OPT`. Crude but `O(n)`.
- **Christofides:** add a **minimum-weight perfect matching** on the `T`-odd-degree vertices. Matching `≤ OPT/2` (the odd vertices, in tour order, split OPT into two matchings) ⇒ total `≤ 3·OPT/2`. The decisive insight that 2 was never the right constant.
- **KKG (2021):** a *randomized* parity fix — sample a spanning tree from the **max-entropy distribution** defined by the Held–Karp LP, then correct parity — beating 3/2 by a tiny constant. The first improvement on Christofides in 45 years.

So MST doubling is best understood as "the matching step done lazily," and the whole subfield as a search for cheaper, structurally-tighter parity corrections.

## 3. Lower bounds, integrality gap, and hardness

- **MST as a lower bound** has worst-case slack: `OPT` can approach `2·M` (path metrics), which is exactly why doubling's ratio is tight at 2. The stronger **Held–Karp / subtour LP** lower bound (same object as in the Held–Karp DP and branch-and-cut siblings) has a conjectured **integrality gap of 4/3** for metric TSP (Goemans); the best proven upper bound on the gap is 3/2 (now slightly below via KKG). Closing the gap to 4/3 is a central open problem.
- **APX-hardness:** metric TSP is APX-hard — **no PTAS** unless P=NP. Explicit inapproximability: NP-hard to approximate within `123/122` (Karpinski–Lampis–Schmied 2015); graph-TSP within `185/184` (Lampis). So constant-factor is the best possible *form* of result for general metrics; the contest is over the constant.
- **Special metrics escape:** **Euclidean** (fixed dimension) and **planar / bounded-genus / bounded-doubling** metrics admit **PTASs** (Arora 1998; Mitchell 1999; Arora–Grigni–Karger–Klein–Woloszyn for planar) — there the `(1+ε)` regime is reachable, and MST doubling is only a coarse fallback.

## 4. Connections beyond TSP

- **2-approx template reused:** the doubling trick gives 2-approximations for related problems — e.g. **metric `s`-`t` path TSP**, and it underlies bounds for **prize-collecting / k-MST** style problems.
- **Steiner-tree / network-design** approximations share the "bound by a tree, repair" structure.
- **DFS-preorder = MST doubling:** the classic "MST → DFS preorder → visit in that order" 2-approximation is the same algorithm (preorder is a shortcut of the doubled Euler tour); a staple of algorithms courses.

## 5. Open problems

1. **The 4/3 conjecture:** is the integrality gap of the subtour-elimination (Held–Karp) LP for metric TSP exactly 4/3? (KKG broke 3/2 by an astronomically small ε; a *practical* improvement remains open.)
2. **Deterministic / clean sub-3/2** metric-TSP approximation (KKG is randomized with a minuscule ε and heavy machinery).
3. **Path TSP** to match cycle TSP (recent Zenklusen 3/2 for `s`-`t` path TSP; further tightening).
4. **Asymmetric TSP** constant: after Svensson–Tarnawski–Végh's first constant and Traub–Vygen's `22+ε`, what is the true approximability of ATSP?
5. Improved approximations for **graph-TSP** (unit weights) below the current `7/5` (Sebő–Vygen).

MST doubling itself is not a research frontier — its ratio (2), tightness, and complexity are fully settled — but it frames all of the above: every result is "how to fix parity better than doubling," and every lower bound is "how slack is the tree/LP bound doubling rests on."

## 6. Links to related problems

- **Direct sibling / upgrade (this registry):** Christofides–Serdyukov (same skeleton, matching instead of doubling, ratio 3/2) — the immediate next entry.
- **Lower-bound machinery:** minimum spanning tree (Prim/Kruskal), Held–Karp / subtour LP bound (shared with Held–Karp DP and Branch-and-cut), minimum-weight perfect matching (Christofides' ingredient).
- **Improvement partners:** 2-opt / Or-opt / Lin–Kernighan local search consume the 2-approx tour as a seed.
- **Paradigm:** approximation algorithms, Eulerian/parity arguments (handshake lemma), metric embeddings, PTAS for geometric TSP, integrality-gap analysis.

## References (seminal → modern)

- Rosenkrantz, Stearns & Lewis (1977). *An analysis of several heuristics for the traveling salesman problem.* (Tree-doubling / DFS 2-approximation and its analysis.)
- Christofides, N. (1976). *Worst-case analysis of a new heuristic for the travelling salesman problem.* (3/2; the better parity fix.) Independently Serdyukov (1978).
- Held, M. & Karp, R. (1970). *The traveling-salesman problem and minimum spanning trees.* (The LP/1-tree bound and its 4/3-conjectured gap.)
- Arora, S. (1998); Mitchell, J. (1999). *PTAS for Euclidean TSP.*
- Goemans, M. (1995). *Worst-case comparison of valid inequalities for the TSP.* (Integrality-gap conjecture context.)
- Karpinski, Lampis & Schmied (2015). *New inapproximability bounds for TSP.* (123/122 metric, 185/184 graph.)
- Sebő, A. & Vygen, J. (2014). *Shorter tours by nicer ears* (graph-TSP 7/5).
- Karlin, Klein & Oveis Gharan (2021). *A (slightly) improved approximation algorithm for metric TSP* (< 3/2).
- Svensson, Tarnawski & Végh (2018); Traub & Vygen (2020). *Constant-factor approximations for ATSP.*

*Uncertainty flags:* the metric-TSP ratio improvement by KKG is real but the ε is astronomically small (no practical impact); whether the Held–Karp integrality gap is 4/3 is **open**. Inapproximability constants (123/122 etc.) are the best *explicit* known, not tight. Tree-doubling's own bounds (ratio 2, `O(n²)`, tightness) are fully settled — it has no live frontier of its own.
