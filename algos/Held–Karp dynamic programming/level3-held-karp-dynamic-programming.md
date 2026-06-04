# Held–Karp dynamic programming — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Shortest Hamiltonian path, Sequential ordering / scheduling with setup costs  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour; time O(n^2·2^n)
> **Used for:** Exact solving and benchmarking for small–medium instances via subset DP
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

Start at city **A**, visit **B**, **C**, **D** once each, return to **A**, shortest total distance. Same table as the brute-force example, so you can compare:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Brute force would total all 6 loops. Held–Karp instead fills a table of **partial answers** and reuses them. The key quantity:

> **g(S, j)** = the length of the shortest path that **starts at A**, **visits exactly the cities in set S**, and **ends at city j** (where j is in S).

We build `g` from small sets up to the full set, then close the loop back to A.

## The steps

**1. Base case — one city visited.** The only way to "visit just {j}" ending at j is to go straight from A:

- g({B}, B) = d(A,B) = 10
- g({C}, C) = d(A,C) = 15
- g({D}, D) = d(A,D) = 20

**2. Two-city sets.** To reach j having visited {i, j}, you must have reached i first (a smaller, already-solved answer), then stepped i→j. Reuse g({i}, i):

- g({B,C}, C) = g({B},B) + d(B,C) = 10 + 35 = 45
- g({B,C}, B) = g({C},C) + d(C,B) = 15 + 35 = 50
- g({B,D}, D) = g({B},B) + d(B,D) = 10 + 25 = 35
- g({B,D}, B) = g({D},D) + d(D,B) = 20 + 25 = 45
- g({C,D}, D) = g({C},C) + d(C,D) = 15 + 30 = 45
- g({C,D}, C) = g({D},D) + d(D,C) = 20 + 30 = 50

**3. Three-city sets {B,C,D}.** Now there's a *choice* of which city you arrived from — take the cheapest. For ending at D having visited all of {B,C,D}, you came from B or from C:

- g({B,C,D}, D) = min( g({B,C},B)+d(B,D), g({B,C},C)+d(C,D) ) = min(50+25, 45+30) = min(75, 75) = **75**
- g({B,C,D}, B) = min( g({C,D},C)+d(C,B), g({C,D},D)+d(D,B) ) = min(50+35, 45+25) = min(85, 70) = **70**
- g({B,C,D}, C) = min( g({B,D},B)+d(B,C), g({B,D},D)+d(D,C) ) = min(45+35, 35+30) = min(80, 65) = **65**

**4. Close the loop.** Add the final step from the last city back to A, and take the best:

- via B: g({B,C,D},B) + d(B,A) = 70 + 10 = **80**
- via C: g({B,C,D},C) + d(C,A) = 65 + 15 = **80**
- via D: g({B,C,D},D) + d(D,A) = 75 + 20 = 95

The minimum is **80** — matching brute force exactly, as it must. (Tracing back the choices gives A→B→D→C→A.)

## Why it works

Held–Karp rests on one observation, the **principle of optimality**:

> If the shortest "visit S, end at j" path passes through city i just before j, then the part *up to i* must itself be the shortest "visit S−{j}, end at i" path.

Why? If there were a cheaper way to reach i over that same set, you could swap it in and get a cheaper path to j — contradicting that yours was shortest. So big answers are always made of *optimal* small answers, which is exactly why reusing the saved `g` values is valid.

## Why it's faster than brute force

Look at step 3: computing g({B,C,D}, ·) **reused** the two-city answers instead of re-walking those paths. Brute force re-walks every prefix inside every full route; Held–Karp computes each "(set, endpoint)" answer **once** and looks it up afterward.

- There are 2ⁿ possible sets and n possible endpoints → about **n·2ⁿ** table entries.
- Filling each entry tries n possible "previous cities" → about **n** work each.
- Total ≈ **n²·2ⁿ**, versus brute force's **n!**.

For n = 20: n²·2ⁿ ≈ 400 million vs n! ≈ 2.4×10¹⁸. That gap is the whole point — though storing n·2ⁿ entries also means Held–Karp eventually runs out of **memory**, which caps it around 20–25 cities.
