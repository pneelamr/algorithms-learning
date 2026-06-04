# Branch-and-bound — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, 0/1 Knapsack, Integer programming, Job scheduling  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour with pruning based on bounds
> **Used for:** General exact search that prunes provably bad subtrees
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

You want the shortest round trip visiting friends Ana, Ben, Cara, and Dan. Brute force tries every complete route. Branch-and-bound is lazier *and* smarter: it grows routes one stop at a time, like a tree of choices — "first go to Ana… or Ben… or Cara…" — and it keeps a running champion (the best complete trip found so far).

Here's the clever part. Before finishing any half-built route, it asks: **"Even in the best possible case, could this partial route ever beat my champion?"** It answers with an *optimistic estimate* — a number it's sure the route can't do better than. If even that optimistic number is worse than the champion, the route is hopeless, so it's abandoned along with **every** route that would have continued from it. One quick check throws away thousands of dead-end routes at once.

## The two moves, named

- **Branch** = split a decision into cases. "The next stop is Ana" vs "the next stop is Ben" vs … Each split makes the problem a little smaller and more specific.
- **Bound** = compute an optimistic best-case score for a partial route (a *lower bound* on how short it could possibly end up). Compare it to the champion. If the optimistic score is already too big, **prune** — cut that branch off entirely.

## The plain walk-through

1. **Find a champion fast.** Make any complete trip (even a rough one) so you have a number to beat.
2. **Grow routes step by step**, branching on the next stop.
3. **At each partial route, compute its optimistic bound.** ("The road so far is 30, and the cheapest possible way to finish from here is at least 25, so this route is *at best* 55.")
4. **Prune the hopeless ones.** If that best-case 55 is already worse than your champion (say 50), drop the whole branch — unexplored continuations and all.
5. **Update the champion** whenever a finished route beats it.
6. When there's nothing left worth exploring, the champion is the **guaranteed best** answer.

## Why it matters

- **Exact, but usually far faster than brute force.** It never settles for "good enough" — it returns the true optimum — yet a good bound lets it skip enormous chunks of the search.
- **The bound is everything.** A *tight, optimistic* estimate prunes aggressively; a lazy one barely helps. Much of the art is designing a good bound (for TSP, a popular one comes from a "minimum spanning tree"–style estimate of the cheapest way to connect the rest).
- **It's a general recipe**, not just for trips. The same branch-then-bound idea solves packing problems, scheduling, and integer math puzzles — anywhere you're searching a giant tree of choices for the best one.

The honest catch: in the *worst* case (a bad bound, or a nasty problem) it can still end up exploring almost everything. But on real problems a smart bound usually prunes so much that it solves instances brute force couldn't dream of.
