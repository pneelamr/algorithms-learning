# Random / farthest insertion — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Multi-start metaheuristic seeding  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound
> **Used for:** Generating diverse starting tours by inserting far/random nodes early
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

Exact optimum is **80** (loop A–B–D–C–A). Let's grow a loop by **farthest** insertion and see how its *selection* differs from the earlier siblings.

## The rule (recap)

- **Selection (farthest):** add the outside city whose distance to the loop, `dist(k,S) = min_{v∈S} d(k,v)`, is **largest**.
- **Placement (shared):** insert into the loop edge minimizing `d(i,k)+d(k,j)−d(i,j)`.

> To keep this run directly comparable to the nearest- and cheapest-insertion artifacts, we seed from the **closest pair A–B**. (Classic farthest insertion instead seeds from the two *farthest-apart* cities, or the convex-hull outline — see the recipe below. The defining feature is the *selection* rule, not the seed.)

## The steps

**Step 0 — seed.** Loop = **A–B–A** (closest pair, length 20). `S = {A, B}`.

**Step 1 — select the FARTHEST outsider from {A, B}.**
- C: `dist(C, S) = min(15, 35) = 15`
- D: `dist(D, S) = min(20, 25) = 20`

Farthest is **D (20)** — so farthest insertion adds **D first**.

> ⚖️ **Compare the family on the very same loop:** *nearest* insertion added **C** (closest, 15); *farthest* adds **D** (20). Cheapest also happened to add D first (lowest insertion cost). Different selection rules, different build orders.

Insert D (cheapest placement, both positions cost 35) → loop **A–B–D–A** (length `10+25+20 = 55`).

**Step 2 — only C remains.** Insert into each edge of A–B–D–A:
- into (A,B): `15+35−10 = 40`
- into (B,D): `35+30−25 = 40`
- into (D,A): `30+15−20 = **25**`

Cheapest is **25**, between D and A → loop **A–B–D–C–A**.

**Result.** A–B–D–C–A = `10 + 25 + 30 + 15 = **80**` — the optimum again, by inserting **D before C** (the far corner first, the nearby city last).

## What about random insertion?

Random insertion uses the **same machinery** but picks the next city by coin-flip. On this tiny 4-city instance the optimum is so dominant that **every insertion order happens to reach 80** — so a single run can't show random's spread here. The diversity only becomes visible at scale: on a 1000-city map, two different random orders give two *different*, non-optimal tours — and that spread is exactly the point, because you then polish each and keep the best.

## The numbered recipe

1. **Seed** the loop. *Farthest:* classically the two farthest-apart cities, or the convex-hull outline. *Random:* a random pair.
2. **Select:** *farthest* → the outside city with the largest `dist(k,S)`; *random* → a uniformly random outside city.
3. **Place:** insert it into the edge minimizing `d(i,k)+d(k,j)−d(i,j)`.
4. **Repeat** until every city is on the loop.

## Why it works

- **Farthest = outline first.** Picking the farthest city each time places the **extreme corners early**, fixing the loop's global shape; the leftover cities are interior and tuck in cheaply. That's why farthest tours look tidiest — and beat nearest/cheapest in practice.
- **Random = diversity.** No bias toward near or far means different runs explore different build orders, producing a *variety* of seeds.
- **Same loop-keeping payoff.** Like the whole family, they keep a complete sub-tour and grow it by the cheapest placement, avoiding the long "stranded city" jumps of the path-growers (nearest neighbor, cheapest-link).

## The catch

Farthest insertion's "best in practice" is **empirical** — surprisingly, its *provable* worst-case guarantee is **weaker** than nearest/cheapest's tight "twice optimal" (it only inherits the family's generic, looser bound). Random insertion's quality **varies run to run** — a feature when you're generating many seeds, a gamble if you only run once. And both reuse the shared table here only for comparison; the family's guarantees are stated for true map-like (metric) distances.

