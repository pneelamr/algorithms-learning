# Brute force enumeration — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem (tiny), small permutation / sequencing problems  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour for very small n
> **Used for:** Teaching and tiny instances; ground-truth baseline to check other methods
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

You live at city **A** and must visit cities **B**, **C**, and **D** exactly once, then return home to **A**. Each road has a length. You want the **shortest round trip**. Here is the distance table (symmetric — the road from B to C is the same length as C to B):

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Brute force enumeration says: **try every possible order of visiting B, C, D, total each loop, keep the smallest.**

## The steps

1. **Lock the start.** Every tour begins and ends at A, so we only shuffle the *middle* cities B, C, D.
2. **List all orderings** of {B, C, D}. There are 3 × 2 × 1 = **6**:
   BCD, BDC, CBD, CDB, DBC, DCB.
3. **Build each full loop** by wrapping A around it: e.g. BCD → A→B→C→D→A.
4. **Add up each loop's length.**
5. **Pick the minimum.**

## Doing the arithmetic

| Order | Full loop | Distance | Total |
|-------|-----------|----------|-------|
| BCD | A→B→C→D→A | 10 + 35 + 30 + 20 | **95** |
| BDC | A→B→D→C→A | 10 + 25 + 30 + 15 | **80** |
| CBD | A→C→B→D→A | 15 + 35 + 25 + 20 | **95** |
| CDB | A→C→D→B→A | 15 + 30 + 25 + 10 | **80** |
| DBC | A→D→B→C→A | 20 + 25 + 35 + 15 | **95** |
| DCB | A→D→C→B→A | 20 + 30 + 35 + 10 | **95** |

The shortest total is **80**, achieved by **A→B→D→C→A** (and its mirror image A→C→D→B→A, which is the same loop walked backwards).

## Why it always works

Brute force gives the **provably optimal** answer for one simple reason: a tour is *just* an ordering of the cities, and we checked **every ordering there is**. There is no route hiding outside the list — the list *is* all routes. So whatever came out smallest can't be beaten.

Notice two things that quietly halved our work, and which matter later:

- **Fixing the start** doesn't lose any tours. A loop has no real "beginning" — A→B→D→C→A is the same cycle no matter which city you call the start — so we may as well always start at A. That's why we permute only the other 3 cities (6 orders) instead of all 4 (24).
- **Each loop equals its reverse.** A→B→D→C→A and A→C→D→B→A are the same road driven in opposite directions, so really there are only **3 distinct tours** here. (Smarter exact methods exploit this; plain brute force often just checks both.)

## Where it goes wrong

The arithmetic was easy with 3 middle cities. But the number of orderings is a **factorial**: with *n* cities you fix one and permute the rest, giving (n−1)! tours.

- 4 cities → 6 tours (what we just did)
- 6 cities → 120 tours
- 10 cities → 362,880 tours
- 13 cities → about 479 million tours

Every city you add multiplies the work, fast. That explosion — correct but unusable beyond a dozen-ish cities — is exactly what motivates the cleverer algorithms in the later levels.
