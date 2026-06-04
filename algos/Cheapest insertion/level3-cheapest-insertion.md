# Cheapest insertion — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Logistics route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by always making the globally cheapest insertion
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

Same 4-city instance as the other artifacts:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Exact optimum is **80** (loop A–B–D–C–A). Let's grow a loop by *cheapest* insertion and watch where it differs from nearest insertion.

## The two rules (recap)

- **Selection + placement together:** among **all** outside cities `k` and **all** loop edges `(i,j)`, pick the single pair minimizing the **insertion cost** `d(i,k) + d(k,j) − d(i,j)`.
- That's the only change from nearest insertion, which first picked the city *closest to the loop* and only then placed it.

## The steps

**Step 0 — start with the closest pair.** Cheapest edge is A–B = 10. Loop = **A–B–A** (length 20).

**Step 1 — score every possible insertion into the A–B loop.**
- Insert **C**: `d(A,C)+d(C,B)−d(A,B) = 15+35−10 = 40`
- Insert **D**: `d(A,D)+d(D,B)−d(A,B) = 20+25−10 = 35`

Cheapest is **D at 35** — so cheapest insertion adds **D first**.

> ⚖️ **Compare:** *nearest* insertion added **C** first (C was the *closest* city to the loop, at 15). Cheapest insertion looks at the *whole insertion cost*, not just closeness — and D, though slightly farther, slots in more cheaply. **Different city, different order.**

Insert D → loop **A–B–D–A** (length `10+25+20 = 55`).

**Step 2 — only C is left; score it into each edge of A–B–D–A.**
- into (A,B): `d(A,C)+d(C,B)−d(A,B) = 15+35−10 = 40`
- into (B,D): `d(B,C)+d(C,D)−d(B,D) = 35+30−25 = 40`
- into (D,A): `d(D,C)+d(C,A)−d(D,A) = 30+15−20 = **25**`

Cheapest is **25**, between D and A. Insert C there → loop **A–B–D–C–A**.

**Result.** A–B–D–C–A = `10 + 25 + 30 + 15 = **80**` — the exact optimum, same as nearest insertion reached, but by inserting **D before C** instead of **C before D**.

## The numbered recipe

1. **Start** with a small loop (the two closest cities).
2. **Score every (outside city `k`, loop edge `(i,j)`) pair** by `d(i,k)+d(k,j)−d(i,j)`.
3. **Insert the globally cheapest** one.
4. **Repeat** until all cities are on the loop.

## Why it works

- **It keeps a complete loop and only ever grows it the least possible amount** — and now "least possible" is taken over *every* choice at once, the most greedy-honest version of the family.
- **The payoff — the same real guarantee:** for map-like ("metric") distances, cheapest insertion is provably **at most twice the optimal tour**, exactly like nearest insertion. Keeping a loop and inserting cheaply is what buys the constant bound — the path-growers (nearest neighbor, cheapest-link) had no such fixed promise.
- **Same destination, different journey.** On this instance both insertion rules land on 80, but the *order* they build it differs — a reminder that the selection rule changes the path, not (here) the answer.

## The catch

Scoring **every** city against **every** loop edge each round is **more work** than nearest insertion (which only hunts the closest city). And "twice optimal" is a worst-case ceiling needing metric distances; real runs land much closer. The next sibling, **farthest insertion**, flips the selection rule to "add the *farthest* city" — and surprisingly tends to beat both nearest and cheapest in practice.

