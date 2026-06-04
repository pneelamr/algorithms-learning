# Nearest insertion — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Drilling / pick-path planning  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by inserting the nearest outside node at lowest cost
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

Same 4-city instance as the other artifacts. Build one loop through **A, B, C, D**:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Exact optimum is **80** (loop A–B–D–C–A). Let's grow a loop by nearest insertion.

## The two rules (recap)

- **Selection:** add the outside city **nearest to the current loop** (nearest to any city already in it).
- **Placement:** insert it into the loop edge that minimizes the **insertion cost** `d(i,k) + d(k,j) − d(i,j)` (how much the loop grows).

## The steps

**Step 0 — start with the closest pair.** The cheapest edge in the table is A–B = 10. Start the loop as **A–B–A** (go A→B and back; a 2-city "loop" of length 20).

**Step 1 — find the nearest outsider to {A, B}.** Distances from each outside city to its nearest loop city:
- C: min(d(C,A)=15, d(C,B)=35) = **15**
- D: min(d(D,A)=20, d(D,B)=25) = **20**

Nearest is **C (15)**. Insert C. The loop A–B has two edges (A→B and B→A). Insertion cost of C:
- between A and B: d(A,C)+d(C,B)−d(A,B) = 15+35−10 = 40
- between B and A (the other copy): d(B,C)+d(C,A)−d(B,A) = 35+15−10 = 40

Either way the loop becomes **A–B–C–A** (length 10+35+15 = 60). (With 2 cities both placements give the same triangle.)

**Step 2 — find the nearest outsider to {A, B, C}.** Only D is left.
- D to loop: min(d(D,A)=20, d(D,B)=25, d(D,C)=30) = **20** (nearest loop city A). Insert D.

Now try inserting D into each edge of loop A–B–C–A:
- between A and B: d(A,D)+d(D,B)−d(A,B) = 20+25−10 = **35**
- between B and C: d(B,D)+d(D,C)−d(B,C) = 25+30−35 = **20**
- between C and A: d(C,D)+d(D,A)−d(C,A) = 30+20−15 = **35**

Cheapest insertion = **20**, between B and C. The loop becomes **A–B–D–C–A**.

**Result.** A–B–D–C–A = 10 + 25 + 30 + 15 = **80** — the exact optimum. Nearest insertion grew the loop straight into the best tour here.

## The numbered recipe

1. **Start** with a small loop (e.g. the two closest cities).
2. **Select**: among cities not in the loop, pick the one nearest to the loop.
3. **Place**: insert it into the loop edge `(i,j)` minimizing `d(i,k)+d(k,j)−d(i,j)`.
4. **Repeat** 2–3 until all cities are in the loop.

## Why it works — and why it's better than the path-growers

- **It keeps a complete loop the whole time** and only ever expands it by the *smallest* possible amount (the cheap-placement rule). Growing outward like this naturally produces round, sensible tours.
- **The selection rule (nearest city)** means each addition is to a city that's genuinely close to what you've built, so insertions stay cheap.
- **The payoff — a real guarantee:** for map-like ("metric") distances, nearest insertion is provably **at most twice the optimal tour**, no matter how many cities. The earlier greedy builders (nearest neighbor, cheapest-link) had no such fixed promise — their worst case got *worse* as cities grew. Keeping a loop and inserting cheaply is what buys the constant bound.

## The catch

The "twice optimal" promise needs distances to be **metric** (a direct hop never longer than a detour — true for real maps), and it's a *worst-case ceiling*: real runs, like this one, usually land much closer to optimal. Insert cost `d(i,k)+d(k,j)−d(i,j)` is never negative exactly because of the triangle inequality — which is also why the loop only ever grows.
