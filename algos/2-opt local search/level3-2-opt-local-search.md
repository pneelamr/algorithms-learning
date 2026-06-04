# 2-opt local search — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; big practical improvement
> **Used for:** Improving a tour by uncrossing pairs of edges until no swap helps
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

The exact optimum is **80** (loop A–B–D–C–A). But unlike the constructive artifacts, 2-opt doesn't *build* a tour — it **improves one you already have.** So let's start from a deliberately bad tour and watch 2-opt fix it.

## Our (bad) starting tour

Take the tour **A–B–C–D–A**:

`d(A,B) + d(B,C) + d(C,D) + d(D,A) = 10 + 35 + 30 + 20 = 95`

That's 95 — worse than the optimal 80. (If you sketch the four cities, this tour visibly *crosses itself*.) Let's 2-opt it.

## The 2-opt move

Pick **two edges** to delete, then reconnect the only other way (which reverses the chunk between them).

Our tour edges are: **A–B**, **B–C**, **C–D**, **D–A**.

**Try deleting B–C and D–A.** Deleting them splits the loop into two paths: `B–A` (the edge A–B read backwards) and `C–D`. The only way to rejoin into one loop is to add the edges **B–D** and **C–A**, which reverses the segment between the cuts. The new tour is:

**A–B–D–C–A**

Let's score the swap by comparing only the edges that changed:
- **Removed:** B–C (35) + D–A (20) = **55**
- **Added:** B–D (25) + C–A (15) = **40**
- **Gain:** 55 − 40 = **15** → improvement! Keep it.

New tour length: `95 − 15 = 80`.

## Check: are we done?

New tour **A–B–D–C–A** = `10 + 25 + 30 + 15 = 80`. Now try every other pair of edges to delete — none of them give a positive gain. So 2-opt **stops**: the tour is *2-optimal*. And here it happens to be the true optimum, 80.

## The numbered recipe

1. **Start** with any complete tour.
2. **Pick two edges** `(t1,t2)` and `(t3,t4)` of the tour.
3. **Reconnect the other way:** delete those two, add `(t1,t3)` and `(t2,t4)`, reversing the segment between them.
4. **Compute the gain** = (removed lengths) − (added lengths). If positive, keep the new tour.
5. **Repeat** 2–4 until no pair of edges gives a positive gain.

## Why it works

- **Only one alternative reconnection.** Once you delete two edges of a loop, there's exactly one *other* way to make a single loop — so each move is a clean either/or decision, and you just check if it's shorter.
- **It removes crossings.** On a map, a tour that crosses itself is always beatable, and the un-crossing move is precisely a 2-opt swap. So 2-opt grinds away every crossing.
- **The gain is local.** You never re-add up the whole tour — just the two edges out and two edges in. That makes each check fast (a handful of additions), which is why you can afford to try many swaps.

## The catch

2-opt stops at a tour where **no single two-edge swap** helps — a *local optimum*. That's often very good, but not always the best possible: some better tours can only be reached by cutting **three or more** edges at once. Fixing *that* is the job of 3-opt and Lin–Kernighan, the next rungs up. (On this tiny 4-city instance 2-opt already nailed the optimum — on big maps it usually lands a few percent above it.)

