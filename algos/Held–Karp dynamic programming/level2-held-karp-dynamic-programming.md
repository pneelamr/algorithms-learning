# Held–Karp dynamic programming — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Shortest Hamiltonian path, Sequential ordering / scheduling with setup costs  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour; time O(n^2·2^n)
> **Used for:** Exact solving and benchmarking for small–medium instances via subset DP
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

You want to visit your friends Ana, Ben, and Cara, then come home — the shortest possible round trip. The slow way (brute force) tries every full route from start to finish. Held–Karp is smarter: it notices that lots of those routes **share the same beginnings**, and it would be silly to recompute those beginnings over and over.

So it keeps a notebook. Each page answers one very specific question:

> "If I've already visited *this exact group* of friends and I'm now standing at *this one*, what's the **shortest** way I could have gotten here?"

Once that page is filled in, you never redo it — you just look it up. Building up from tiny groups to bigger groups, you reach the full trip having done each piece of work only once.

## The plain walk-through

1. **Start small.** The shortest way to reach Ana having visited *only* Ana is just: Home → Ana. Same for Ben, for Cara. Write those down.
2. **Grow the group by one.** Now ask: shortest way to reach Cara having visited {Ana, Cara}? It must have arrived at Cara *from* Ana — and you already wrote down the best "reach Ana" answer. So you reuse it and just add the Ana→Cara step.
3. **Keep growing.** For each bigger group of visited friends and each possible "last friend you're standing at," combine an already-solved smaller answer with one more step. Always keep the cheapest.
4. **Close the loop.** When the group is *everyone*, take the best "reach the last friend having visited all of them" and add the final step back Home. The smallest total is the perfect answer.

The trick in step 2–3 is **reuse**: every answer is built from smaller answers you already saved, so you never start a route from scratch.

## Why this is a big deal

- **It's still exact.** Like brute force, Held–Karp always finds the truly shortest tour — no guessing.
- **It's dramatically faster.** Brute force's work grows like *n!* (for 20 cities, that's billions of billions of routes). Held–Karp's work grows like *n²·2ⁿ* — still big, but for 20 cities that's a few million steps instead of astronomically many. That moves the "exactly solvable" limit from about **12 cities up to around 20**.
- **The catch is memory.** To get this speed it has to *store* an answer for every group-and-endpoint combination, and there are 2ⁿ groups. So it trades a lot of memory for the speed-up — which is why it shines for small-to-medium problems, not huge ones.

Held–Karp is the classic example of **dynamic programming**: break a hard problem into overlapping smaller pieces, solve each piece once, write it down, and reuse it.
