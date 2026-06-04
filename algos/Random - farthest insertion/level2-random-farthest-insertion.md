# Random / farthest insertion — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Multi-start metaheuristic seeding  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound
> **Used for:** Generating diverse starting tours by inserting far/random nodes early
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## Same family, the last two knobs

Still the **insertion family**: keep a complete loop and grow it, always slotting each new city into the loop edge that adds the least length (`d(i,k) + d(k,j) − d(i,j)`). Only the **selection** rule — *which* city to add next — changes:

- **Nearest insertion:** the city **closest** to the loop.
- **Cheapest insertion:** the city+spot with the **smallest insertion cost**.
- **Farthest insertion:** the city **farthest** from the loop.
- **Random insertion:** a city chosen **at random**.

This file covers the last two — grouped because they're the ones used to make **good or diverse starting tours**, not because they share a rule.

## Why "farthest" of all things?

It sounds backwards to add the *farthest* city first. Here's the intuition: the farthest cities are the **corners and extremes** of the map. If you place those early, you immediately lay down the loop's **overall outline** — its big skeleton. Everything left over is an *interior* city sitting near the skeleton, so each later insertion is a tiny, cheap local fix.

Compare nearest insertion, which adds nearby cities first and only meets the far corners at the very end — by then the loop is "set," and jamming a far corner in late can force an awkward long detour. **Farthest insertion avoids that by doing the hard, structure-defining placements first.**

The punchline (the surprise this whole family has been building to): farthest insertion has a **worse paper guarantee** than nearest/cheapest — yet it usually produces the **best tours** of the family. Outline-first just works.

## Why "random"?

Random insertion picks the next city by coin-flip. One run gives a decent tour; the real value is that **different random orders give different tours**. So you can run it many times to get a *spread* of starting tours — exactly what you want when you're going to polish each one (with 2-opt or Lin–Kernighan) and keep the best, or feed a **multi-start** method. Random insertion is also surprisingly strong on its own.

## The plain walk-through (both)

1. **Start** with a small loop (farthest insertion classically starts from the two *farthest-apart* cities, or the convex-hull outline; random insertion from a random pair).
2. **Select** the next city — the **farthest** from the loop, or a **random** one.
3. **Insert it cheaply** into the loop edge that grows the loop least.
4. **Repeat** until all cities are on the loop.

## Why it matters

- **Best practical tours in the family (farthest).** Outline-first construction beats nearest/cheapest in real runs despite a weaker worst-case promise — the family's headline "worst-case ≠ average-case" lesson.
- **Diversity for free (random).** Cheap, repeatable, and gives many different seeds — ideal for "build many, polish all, keep the best."
- **Great seeds for the polishers.** Both feed straight into the local-search cluster (2-opt, 3-opt, Lin–Kernighan, chained Lin–Kernighan): a farthest-insertion seed often lands a polisher in a better basin than a nearest-insertion seed.

The honest caveat: farthest insertion's "best in practice" is **empirical** (no better guarantee than the generic insertion bound), and random insertion's quality varies run to run — that variance is the *point* when seeding multi-start methods, but it means a single random run is a gamble.

