# Branch-and-cut (ILP / Concorde-style) — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Mixed-integer linear programs, Vehicle Routing Problem, Max-cut  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour; state-of-the-art exact performance
> **Used for:** Research-grade exact solving by combining branching with cutting planes
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

You're planning the shortest single loop that visits every city once. Here's a way to *estimate* the answer cheaply: relax the rules. Instead of demanding one big tour, just ask for the cheapest set of roads where every city has a road coming in and a road going out. That's an easy puzzle a computer can solve fast — but it cheats: the cheap answer is often a bunch of **small disconnected loops** instead of one grand tour.

So you add a rule: "the cities in that little loop must connect to the rest." This rule is a **cut** — it slices the cheating answer out of the running without removing any real, honest tour. You re-solve, the answer cheats in a new way, you add another cut, and so on. Each cut drags the cheap estimate upward toward the true shortest tour.

Sometimes cutting alone gets stuck (the answer is *almost* a tour but has a fractional, half-a-road kind of cheat). Then you **branch**: split into two worlds — "this road is used" vs "this road is not used" — and keep cutting inside each. Cutting + branching together is **branch-and-cut**.

## The two tools

- **Cut (cutting plane):** a new rule that forbids the *current* cheating answer while keeping every legal tour. It tightens your estimate without branching.
- **Branch:** when cuts run out of steam, split the problem into cases and recurse — exactly like branch-and-bound.

## The plain walk-through

1. **Solve the easy, relaxed version** (allow fractions and ignore the "one big loop" rule). You get a cheap lower estimate of the answer.
2. **Look at the cheat.** Disconnected mini-loops? A half-used road? Find a rule that forbids *this specific* cheat but no real tour.
3. **Add the cut and re-solve.** The estimate rises. Repeat — pile on cuts as long as they help.
4. **If cuts stall, branch** into "use this road / don't use this road," and go back to cutting in each branch.
5. **Stop** when the estimate meets an actual tour you've found — now you've *proven* it's the best possible.

## Why it matters

- **It's exact** — it returns the provably shortest tour, with a certificate, not just a good guess.
- **It's the champion.** This is how the famous **Concorde** solver cracked a tour through ~85,000 cities to *proven* optimality. Plain "try-everything" methods top out around a dozen cities; the dynamic-programming method around twenty; branch-and-cut reaches **tens of thousands**.
- **The cuts are the secret.** Smart rules ("subtour" and "comb" inequalities, found over decades of math) make the cheap estimate so accurate that very little branching is needed. The better the cuts, the less searching.

Branch-and-cut is branch-and-bound supercharged: same branch-and-prune skeleton, but with a constantly-sharpening estimate built from clever geometric rules. The honest catch is the same — in the absolute worst case it can still blow up — but on real maps it's astonishingly powerful.
