# Lin–Kernighan heuristic — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; near-optimal in practice
> **Used for:** Variable-depth edge swaps producing near-optimal tours
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The question that leads here

2-opt always cuts **2** edges; 3-opt always cuts **3**. Each step up gives better tours but costs a lot more, and you have to pick the number in advance. The natural question: *why fix the number at all?* Lin–Kernighan (LK) answers it — **it chooses how many edges to swap on the fly, every single move.**

## The analogy, expanded

Think of building one swap as a **chain**:
1. **Break** an edge of the tour — now you have a loose end.
2. From that loose end, **add** a new, shorter edge to some other city. Adding it forces you to **break** that city's old edge (or you'd have a city with too many connections).
3. That gives a *new* loose end — so you can **add** another short edge, which **breaks** another... and so on.

At each link of the chain LK keeps a running tally of **"how much have I saved so far?"** (lengths broken minus lengths added). It also checks, at each step, *"if I tied the loose end back into a finished tour right now, would it be shorter than what I started with?"* It remembers the **best** such finished tour seen anywhere along the chain.

When extending the chain stops looking promising (the running savings would go negative), LK **stops, takes the best tour it found**, and starts a fresh chain from there. Repeat until no chain finds any improvement.

## The plain walk-through

1. **Start with a tour** (from any constructor, often after a quick 2-opt).
2. **Begin a chain** at some city: break one of its edges.
3. **Greedily extend:** repeatedly add a short edge and break the one it forces, tracking cumulative savings; at each depth, note the best *closed-up* tour.
4. **Stop** when extending can't keep the running savings positive; adopt the best closed tour found.
5. **Repeat** from different starting cities until nothing improves.

## Why it matters

- **Best-in-class quality.** Because each move is effectively a *variable*-depth k-opt move — sometimes a 2-swap, sometimes reaching 5, 6, or deeper — LK escapes traps that fixed 2-opt and 3-opt fall into. On real benchmarks it lands **~1–2% from the best tour possible**, far better than the fixed methods, for comparable time.
- **It adapts the effort to the tour.** Easy spots get shallow moves; hard tangles get deep ones. You don't pay `n³` everywhere like 3-opt; you pay for depth only where it helps.
- **It's the practical gold standard.** The world's strongest TSP tools (LKH, and the upper-bound engine inside the exact Concorde solver) are LK at their core.

The honest caveat: LK is **cleverer to implement** than 2-opt/3-opt — the chain has rules about which edges you're allowed to add and break so it always *can* close back into a real tour, plus a bit of backtracking. And it **still** has no guarantee and still gets stuck at a local optimum eventually. Escaping *that* last trap — by kicking the tour and re-running LK — is the final method in this cluster: **chained Lin–Kernighan.**

