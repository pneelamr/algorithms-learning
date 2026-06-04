# Christofides–Serdyukov — Level 3: High-school student

> **Example problems:** Metric Traveling Salesman Problem, Vehicle Routing Problem, Logistics / delivery route planning  ·  **Type:** Approximation  ·  **Guarantee:** At most 1.5× optimal (metric TSP)
> **Used for:** Balanced quality/speed approximation with a worst-case bound
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

Same 4-city metric instance as the other artifacts. Start at **A**, visit **B, C, D**, return — approximately, with a 1.5× guarantee:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Exact optimum is **80** (A→B→D→C→A). Let's run Christofides and check the bound.

## The steps

**1. Build the minimum spanning tree (MST).** Cheapest 3 edges connecting all four with no cycle (same as the MST-doubling artifact):

- A–B = 10, A–C = 15, A–D = 20. **MST = star at A, weight 45.**

**2. Find the odd-degree vertices** (cities touched by an odd number of tree edges):

- A has degree 3 (edges to B, C, D) → **odd**
- B has degree 1 (just A–B) → **odd**
- C has degree 1 → **odd**
- D has degree 1 → **odd**

So **all four** {A, B, C, D} are odd. (Note: the count of odd vertices is 4 — even, as it always must be.)

**3. Minimum-weight perfect matching on the odd vertices.** Pair up {A, B, C, D} into 2 pairs so the added distance is smallest. The three ways to pair four cities:

- {A–B, C–D} = 10 + 30 = **40**
- {A–C, B–D} = 15 + 25 = **40**
- {A–D, B–C} = 20 + 35 = 55

Cheapest matching = **40** (say {A–B, C–D}).

**4. Combine tree + matching into a multigraph.** Edges now: A–B (tree) + A–B (matching) + A–C + A–D + C–D (matching). Check degrees:

- A: B, B, C, D → degree 4 (even ✓)
- B: A, A → degree 2 (even ✓)
- C: A, C–D → degree 2 (even ✓)
- D: A, C–D → degree 2 (even ✓)

Every vertex even ⇒ an **Euler tour** (one closed walk using every edge once) exists.

**5. Walk an Euler tour and shortcut.** One Euler walk: A → B → A → C → D → A. First-appearance order: **A, B, C, D**. Shortcutting repeats gives the tour **A → B → C → D → A**.

- Cost = 10 + 35 + 30 + 20 = **95.**

## Checking the guarantee

- Tour = 95, optimum = 80 → ratio ≈ **1.19×**, under the 1.5× promise. ✓
- The bound proof says: tour ≤ MST + matching = 45 + 40 = **85**, and 85 ≤ 1.5 × OPT = 120. Our 95 is a particular Euler/shortcut choice; a luckier traversal (visiting D before C) gives A→B→D→C→A = **80**, the exact optimum. As with MST doubling, **1.5× is a worst-case ceiling, not a target** — real runs land well under it.

## Why it's better than doubling

Compare the two parity fixes on this very instance:

- **MST doubling** adds a full copy of the tree: extra cost = 45. Total structure ≤ 2·MST = 90.
- **Christofides** adds only the matching on odd vertices: extra cost = 40. Total structure ≤ MST + matching = 85.

Christofides spent **less** to fix parity (40 vs 45), and in general the gap is bigger — that's the whole point. The reason the *guarantee* improves to 1.5× is a beautiful fact: the minimum matching on the odd vertices always costs **at most half the optimal tour**.

## Why the matching is at most ½·OPT (the intuition)

Take the optimal tour and look only at the odd-degree vertices, in the order the tour visits them. Connecting every other consecutive pair gives **two** different perfect matchings of those vertices (the "odd-position" links and the "even-position" links). Together they cost no more than the whole optimal tour (by the triangle inequality, jumping straight between consecutive odd vertices is no longer than the tour segments between them). Since the two matchings together cost ≤ OPT, the **cheaper** of the two costs ≤ OPT/2 — and the *minimum* matching is at least as cheap as that. So:

```
tour ≤ MST + matching ≤ OPT + OPT/2 = 1.5 · OPT.
```

## The catch (same as before)

Distances must be **metric** (triangle inequality) — otherwise shortcutting could lengthen the trip and the 1.5× promise breaks. For real maps it always holds.
