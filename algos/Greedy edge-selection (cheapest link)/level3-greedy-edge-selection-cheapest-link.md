# Greedy edge-selection (cheapest link) — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Circuit / cable layout  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; depends on instance
> **Used for:** Constructing a tour by adding cheapest valid edges first
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works (and fails). See sibling files for other levels.

---

## The problem, concretely

Same 4-city instance as the other artifacts. Visit all of **A, B, C, D** in one loop:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Exact optimum is **80** (the loop A–B–D–C–A). Unlike nearest neighbor, cheapest-link has **no start city** — it picks edges by price from the whole map.

## The rules (recap)

A tour = a single loop where every city has **exactly 2 edges**. So when adding edges cheapest-first, reject any edge that would:
- give a city a **3rd edge** (degree 3), or
- **close a loop early** (form a cycle before all 4 cities are linked).

## The steps

First, list all 6 edges sorted by cost:

| Edge | Cost |
|------|------|
| A–B  | 10 |
| A–C  | 15 |
| A–D  | 20 |
| B–D  | 25 |
| C–D  | 30 |
| B–C  | 35 |

Now walk down the list:

1. **A–B (10)** — A and B both have 0 edges. Legal. **Add.** Degrees: A=1, B=1. Chains: A–B.
2. **A–C (15)** — A has 1, C has 0. Adding makes A=2, C=1, no early cycle. Legal. **Add.** Chain: B–A–C.
3. **A–D (20)** — but A already has **2 edges** (to B and C). Adding a third is illegal (degree 3). **Skip.**
4. **B–D (25)** — B has 1, D has 0. Makes B=2, D=1. Does it close a loop early? Current chain is B–A–C; adding B–D extends it to D–B–A–C. No premature cycle. Legal. **Add.** Chain: D–B–A–C. Degrees: A=2, B=2, C=1, D=1.
5. **C–D (30)** — C has 1, D has 1. Adding C–D connects the two **loose ends** of the chain D–B–A–C... which **closes the loop** — and all 4 cities are now included, so this is the *allowed* final closing edge. Legal. **Add.** 

The loop is complete: **C–D–B–A–C**, i.e. the tour **A–B–D–C–A**.

## Total it up

A–B (10) + B–D (25) + D–C (30) + C–A (15) = **80.** 

On this instance cheapest-link nails the optimum (80) — same loop as the exact methods. The greedy bargains happened to assemble perfectly. (We never needed the expensive B–C=35 edge — it sat at the bottom of the list and got skipped as redundant.)

## The numbered recipe

1. **Sort all edges** cheapest → most expensive.
2. Go through them in order. **Add an edge** only if both endpoints currently have fewer than 2 chosen edges **and** it doesn't close a cycle (unless it's the final edge completing the full tour).
3. Otherwise **skip** it.
4. Stop once `n` edges are chosen — they form one complete loop through all cities.

## Why it works — and why it isn't trustworthy

- **Why it often works:** by always taking globally cheap edges, the tour is built mostly from genuine bargains, so it tends to be reasonable.
- **Why it can fail:** the rules can force you to **skip cheap edges** (because a city is full or a loop would close early), and then *much later* you're stuck joining leftover chains with whatever edges remain — often expensive ones. Those forced end-game edges are cheapest-link's version of nearest neighbor's "stranded city" problem. There is **no guarantee**: on bad layouts the tour can be far from optimal, and the worst-case gap grows with the number of cities.

## How it differs from nearest neighbor

- **Nearest neighbor** grows *one chain* from a start city, always stepping to the nearest unvisited city. Its result depends on where you start.
- **Cheapest-link** grows *several chains at once* from the cheapest edges anywhere, with no start city, merging them into one loop. It can grab good edges nearest neighbor would never reach — but can also leave scattered chains that are awkward to stitch together at the end.
