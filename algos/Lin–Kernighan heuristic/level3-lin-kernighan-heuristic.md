# Lin–Kernighan heuristic — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; near-optimal in practice
> **Used for:** Variable-depth edge swaps producing near-optimal tours
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The idea in one picture: a chain of break–add steps

Lin–Kernighan builds **one** improving move as a **chain**. It alternates:

- **break** a tour edge `x_i` (saves its length), then
- **add** a non-tour edge `y_i` (costs its length),

always keeping a running **cumulative gain** `G = (lengths broken) − (lengths added)`. After each *add*, it checks: *"close the chain back into a full tour now — is it shorter?"* It keeps the **best** closed tour found along the way.

The rule that keeps it sane: only extend the chain while `G` stays **positive** ("partial gain criterion"), and always add the new edge `y_i` to a **near** city so adds stay cheap.

## A worked chain (small numbers)

Suppose at some city `t1` the tour uses a long edge `t1–t2` of length **30**. LK starts a chain by breaking it.

**Step 1 — break `x₁ = (t1,t2)`, add `y₁ = (t2,t3)`.**
Pick `t3` near `t2`: say `d(t2,t3) = 8`.
Running gain so far: `g₁ = 30 − 8 = 22` (positive — good).
*Try closing now:* tie `t3` back to `t1`. Suppose that closing edge costs 20, and we had to break `t3`'s old edge of length 12 — the net closed tour is shorter by `g₁ − (close cost − broken) = …`; LK records this candidate.

**Step 2 — break `x₂ = (t3,t4)` (length 12), add `y₂ = (t4,t5)` (length 6).**
Running gain: `G = 22 + (12 − 6) = 28` (still positive — keep going).
*Try closing now:* tie `t5` back to `t1`; suppose that yields a tour **15 shorter** than the start. Record it as the best so far.

**Step 3 — try to extend again.** The next best add from `t5` costs 25 while the edge it would break is only 9, so the running gain would drop to `28 + (9 − 25) = 12`… still positive, but the *closed* tour at this depth is worse than Step 2's. LK keeps exploring a little (with limited backtracking), but here further steps don't beat Step 2.

**Stop.** The running gain finally can't stay positive (or depth limit hit). LK **commits the best closed tour it saw — the Step 2 one, 15 shorter** — and starts a fresh chain elsewhere.

The point: LK did a **4-edge (depth-2) move here**, but on a different part of the tour the same procedure might stop at depth 1 (a plain 2-opt move) or push to depth 6. *The depth is chosen by the running gain, not fixed in advance.*

## The numbered recipe

1. **Start** with a tour. Pick a city `t1` and **break** one of its edges.
2. **Add** a short edge `y_i` from the current loose end to a near city; **break** the tour edge that forces; update cumulative gain `G`.
3. **At each step, try closing** into a full tour and record it if it's the best seen.
4. **Keep extending** while `G > 0` (with a little backtracking over the few best choices).
5. **Stop and commit** the best closed tour found; repeat from other cities until nothing improves.

## Why it works

- **Variable depth beats fixed depth.** By following the "still saving money" trail, LK automatically does a deep move where a deep move is needed and a shallow one where that's enough — getting 3-opt-or-better quality without always paying 3-opt's cost.
- **The positive-gain rule is the secret.** There's a neat fact: if the *total* of a sequence of break−add steps is positive, then some prefix of it, reordered, has *every* partial sum positive — so insisting on "keep `G` positive" never makes you miss an overall-improving chain. That's why greedily following positive gain is safe.
- **Always closeable.** The break/add rules are designed so the loose end can always be tied back into a legal single tour, so every step has a real candidate tour to compare.

## The catch

LK is **trickier to code** than 2-opt/3-opt (the chain must obey rules so it can always close, plus backtracking), and it **still** ends at a local optimum with **no guarantee**. To squeeze out the last bit, you **kick** the tour with a special 4-edge "double bridge" and run LK again — that's **chained Lin–Kernighan**, the next and final method in this cluster.

