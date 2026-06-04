# Branch-and-bound — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, 0/1 Knapsack, Integer programming, Job scheduling  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour with pruning based on bounds
> **Used for:** General exact search that prunes provably bad subtrees
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

Same 4-city instance as the Brute-force and Held–Karp artifacts, so you can compare. Start at **A**, visit **B**, **C**, **D** once, return to **A**, shortest total:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

We know the answer is **80** (tour A→B→D→C→A). Let's see how branch-and-bound reaches it *without* totaling all 6 tours.

## The ingredients

- **Search tree:** the root is "at A, nothing visited." Each level picks the next city. A leaf is a full tour.
- **Incumbent:** the best complete tour found so far (our champion). Start it at `+∞`, or seed it with a quick tour.
- **Lower bound** of a partial route: an *optimistic* estimate = (distance travelled so far) + (a guaranteed-too-cheap estimate of finishing). We'll use a simple bound: cost so far **+** the cheapest single edge leaving the current city to any unvisited city **+** the cheapest edge from each remaining city back into the unvisited set / home. (Any rule that never *overestimates* the true completion works; simpler ones prune less.)

For clarity we'll use an easy, valid bound: **cost-so-far + cheapest available edge out of the current city.** It under-counts the real finish, so it's safe (optimistic), just not super tight.

## A walk-through

**Step 0 — seed a champion.** Try the greedy tour A→B(10)→D(25)→C(30)→A(15) = **80**. Incumbent = 80. (Lucky — it's actually optimal, but B&B doesn't know that yet and must *prove* it.)

**Step 1 — branch on the first stop from A.** Three children: go to B, C, or D.

- **Branch A→C:** cost so far 15. Cheapest edge out of C to an unvisited city is C→D = 30. Optimistic bound ≥ 15 + 30 + (must still reach the last city and return) ≥ 15+30+25+10 = 80. It ties the incumbent at best — and any *strict* improvement is impossible, so this branch cannot beat 80. **Prune** (or explore and confirm no improvement). 
- **Branch A→D:** cost so far 20. Cheapest out of D is D→B = 25 → already 45, and finishing needs two more edges (≥ 35 + 15). Optimistic ≥ 20+25+35+15 = 95 > 80. **Prune** the entire A→D subtree — both tours under it (A→D→B→C→A and A→D→C→B→A) are discarded **uncomputed**.
- **Branch A→B:** cost so far 10, promising. Keep exploring.

**Step 2 — under A→B, branch on the next stop (C or D).**

- **A→B→C:** cost 10+35 = 45. Finish needs C→D→A = 30+20 = 50 → total would be 95 > 80. **Prune.**
- **A→B→D:** cost 10+25 = 35. Only D→C→A left = 30+15 = 45 → total **80**. Ties incumbent; not an improvement, so the incumbent stays 80 (now confirmed reachable).

**Step 3 — nothing left unpruned.** Every branch was either explored or provably ≥ 80. The incumbent **80** is therefore **optimal** — proven, not just found.

## What just happened

Branch-and-bound **never totaled the A→D tours at all** — one bound check (95 > 80) erased that whole subtree. On 4 cities the savings look modest, but the pruning compounds: on 15 cities a good bound can discard the vast majority of the (15−1)!/2 ≈ 43 billion tours, letting B&B finish in a flash while brute force chokes.

## Why it's still always correct

The golden rule: **only prune a branch when its lower bound is ≥ the incumbent.** Because the bound is *optimistic* (it can never claim a route is cheaper than it really is), a pruned branch genuinely cannot contain anything better than the champion we already hold. So nothing optimal is ever thrown away — B&B returns the exact best tour, just faster. The looser the bound, the less it prunes; the tighter the bound, the faster it goes — but correctness never depends on tightness, only on the bound staying optimistic.
