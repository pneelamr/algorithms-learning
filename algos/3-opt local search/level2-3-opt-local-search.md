# 3-opt local search — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; better than 2-opt, slower
> **Used for:** Improving a tour via 3-edge reconnections for higher quality than 2-opt
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## Where this picks up

2-opt improves a tour by deleting **two** edges and reconnecting the one other way (which flips a chunk of the route around). It's great, but it gets **stuck**: it reaches a tour where no single two-edge swap helps, even though a better tour exists. 3-opt is the natural next step — same "improve an existing tour" idea, but cutting in **three** places instead of two.

## The analogy, expanded

Cut your loop in **three** spots and you get **three pieces** of road. Now there isn't just one way to reconnect — there are several. Some of them are just 2-opt moves in disguise (flip one piece). But three of the reconnections are genuinely *new*, including the most useful one:

- **Move a whole chunk somewhere else** in the tour — *without flipping it.* 2-opt can never do this (its only move reverses a segment). So a city or a run of cities that's sitting in a bad spot can be **relocated** to a better one.

That extra power is why 3-opt finds shorter tours than 2-opt — it can escape places where 2-opt is frozen.

## The plain walk-through

1. **Start with a tour** (often a 2-opt'd tour, so you only spend the pricier 3-opt effort where 2-opt already gave up).
2. **Pick three edges** to delete. The tour falls into three pieces.
3. **Try the ways to reconnect** the three pieces into one loop (there are 7 non-trivial ones).
4. **Keep the shortest** reconnection if it beats what you had.
5. **Repeat** until no choice of three edges gives an improvement → the tour is *3-optimal*.

## Why it matters

- **Higher quality.** A 3-optimal tour is at least as good as a 2-optimal one (every 2-opt move is also available to 3-opt), and usually noticeably better — typically a few percent closer to the best possible.
- **It can relocate, not just reverse.** The "pick up a chunk and drop it elsewhere" move fixes mistakes 2-opt structurally cannot.
- **It's the bridge to the best methods.** Once you see that "more cuts = more power but more cost," the obvious question is: *why fix the number of cuts at all?* That question leads straight to **Lin–Kernighan**, which decides how many cuts to make on the fly.

The honest caveat: more power costs more time. Checking all triples of edges is much slower than checking pairs (think "cubed" instead of "squared"), and 3-opt **still** has no guarantee and still stops at a local optimum — just a better one. There's also a famous move, the **"double bridge,"** that needs *four* cuts and that even 3-opt can't make — remember that; it's the secret behind the very best method in this cluster.

