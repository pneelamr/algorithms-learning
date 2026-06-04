# Cheapest insertion — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Logistics route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by always making the globally cheapest insertion
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## Same family, one knob turned

This is the **insertion family** again — keep a complete little loop and grow it by slotting in one city at a time. Every family member shares the same **placement** rule: a city always goes into the loop edge where it adds the least length, `d(i,k) + d(k,j) − d(i,j)`. They differ only in the **selection** rule: *which* city to add next.

- **Nearest insertion** asked: which outside city is *closest to the loop*? (Then place it cheaply.)
- **Cheapest insertion** asks a bigger question: across **all** outside cities *and* **all** loop positions at once, which single (city, position) pair has the **smallest insertion cost**? Do that one.

So nearest insertion decides *who* first and *where* second; cheapest insertion decides *who and where together*, always taking the globally cheapest tie-in available right now.

## The plain walk-through

1. **Start tiny.** Begin with a 2-city loop (often the closest pair).
2. **Score every possible insertion.** For each outside city `k` and each loop edge `(i,j)`, compute the cost to slot `k` there: `d(i,k) + d(k,j) − d(i,j)`.
3. **Do the cheapest one.** Pick the (city, edge) with the smallest score and insert it.
4. **Repeat** until every city is on the loop.

The difference from nearest insertion is subtle but real: cheapest insertion might *skip* a city that's close to the loop if slotting it in anywhere is still expensive, in favor of a slightly-farther city that happens to fit into a gap very cheaply.

## Why it matters

- **Same guarantee, smarter greed.** Like nearest insertion, on normal "map-like" distances cheapest insertion is provably **never worse than twice the best possible tour** — a fixed promise that doesn't decay as cities pile up. But it makes the *globally* cheapest choice each step, which often gives tidier tours in practice.
- **It's a natural "just do the best-looking move" idea.** Of all the family rules, "always take the cheapest insertion anywhere" is the most obviously greedy and the easiest to justify to someone.
- **Workhorse for route-building.** This "cheapest tie-in, repeated" template is exactly how a lot of real delivery- and logistics-route builders grow routes.

The honest caveat: looking at *every* city against *every* loop position each step is **more work** than nearest insertion's "find the closest city" — more bookkeeping per round. And "twice the best" is still a worst-case ceiling needing map-like distances; real runs land much closer. Curiously, the family member with the *weakest* worst case — **farthest insertion** — usually produces the *best* tours, which is the surprise the next sibling explores.

