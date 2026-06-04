# 3-opt local search — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; better than 2-opt, slower
> **Used for:** Improving a tour via 3-edge reconnections for higher quality than 2-opt
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## Why we need a bigger example

On the shared 4-city instance (A, B, C, D; optimum 80), **2-opt already reaches the optimum** — and with only 4 cities, cutting in 3 places is basically "try everything." So 3-opt's *extra* power doesn't show up there. To actually see 3-opt do something 2-opt can't, we need a tour that is **stuck for 2-opt** but fixable by relocating a chunk.

## A 6-city instance where 2-opt is stuck

Cities on a line-ish layout with one city, **X**, sitting in the wrong place. Consider the tour

**1 – 2 – X – 3 – 4 – 5 – 1**

where X really belongs between 4 and 5, but it got stranded between 2 and 3. Suppose the distances make this tour cost **100**, and suppose **no single 2-opt move (reverse one segment) lowers it** — every reversal that pulls X out also flips a good chunk of the route the wrong way, so 2-opt is frozen at 100.

## The 3-opt move that fixes it: relocate the chunk

Delete **three** edges: `(2, X)`, `(X, 3)`, and `(4, 5)`. That breaks the tour into three pieces:
- piece P1: `5 – 1 – 2`
- piece P2: `X` (just the one city)
- piece P3: `3 – 4`

Now reconnect by **dropping X between 4 and 5** *without reversing anything*:

**1 – 2 – 3 – 4 – X – 5 – 1**

Score it by the edges that changed:
- **Removed:** `d(2,X) + d(X,3) + d(4,5)`
- **Added:** `d(2,3) + d(4,X) + d(X,5)`

With X genuinely belonging next to 4 and 5, the added edges are short and the removed ones were long — say removed = 55, added = 35, so the **gain is 20** and the tour drops from 100 to **80**. Crucially, this move **moved a piece to a new place without flipping any segment** — something 2-opt can *never* do, which is exactly why 2-opt was stuck and 3-opt wasn't.

## The seven ways to reconnect three pieces

When you cut three edges you get three paths; reconnecting them into one loop has **7 non-trivial outcomes**. Roughly:
- **3 of them** are just 2-opt moves (reverse one of the three pieces) — nothing new.
- **3 of them** reverse two pieces or combine reversals.
- **1 of them** is the "**pure**" 3-opt move: **relocate a piece to a new spot without reversing it** (the move we just used).

3-opt tries all 7 and keeps the best.

## The numbered recipe

1. **Start** with a tour (often already 2-optimal, so 3-opt only works where 2-opt quit).
2. **Pick three edges** to delete → three pieces.
3. **Try the 7 reconnections** into a single loop.
4. **Compute each gain** = (removed lengths) − (added lengths); keep the best improving one.
5. **Repeat** until no triple of edges improves the tour → *3-optimal*.

## Why it works

- **More reconnections = more escapes.** With three cuts you can do everything 2-opt could (so you're never worse) *plus* the relocate-a-chunk move, so you slip out of places where 2-opt is frozen.
- **It still only adds up the changed edges.** Each of the 7 options compares a few removed edges against a few added ones — no re-summing the whole tour — so each check stays quick.

## The catch

3-opt is much **slower**: instead of checking all *pairs* of edges (`n²`-ish), you check all *triples* (`n³`-ish). And it **still** stops at a local optimum (now "3-optimal") with **no guarantee** of being best. Worse, there's a special **four-cut "double-bridge" move** that 3-opt *also* can't make — escaping *that* trap is what Lin–Kernighan and chained Lin–Kernighan are for, the next two rungs up.

