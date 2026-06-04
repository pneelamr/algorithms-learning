# Branch-and-cut (ILP / Concorde-style) — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Mixed-integer linear programs, Vehicle Routing Problem, Max-cut  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour; state-of-the-art exact performance
> **Used for:** Research-grade exact solving by combining branching with cutting planes
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The idea in one picture

A tour is hard to demand directly, so we describe it with **inequalities** a computer can optimize over, then *fix up* the description when it lets cheating answers through. Two ideas drive it:

- **Relaxation:** drop hard constraints and allow fractional values, so the problem becomes a fast **Linear Program (LP)**. Its answer is a *lower bound* (it can only be cheaper than the real tour, since we removed rules).
- **Cutting plane:** when the LP answer isn't a real tour, add one inequality that **forbids that specific bad answer** but keeps every real tour legal. Re-solve; repeat.

## Setting it up for TSP

Put a variable `x_e` on every edge `e`: `x_e = 1` if the tour uses edge `e`, `x_e = 0` if not. The real problem wants each `x_e` to be 0 or 1; the *relaxation* lets `x_e` be any fraction in `[0, 1]`.

**Degree constraints** (every city touched by exactly two tour edges):
```
for each city v:   sum of x_e over edges e touching v  =  2
```

Minimize total length `Σ d_e · x_e` subject to those. Fast to solve — but the answer can cheat in two ways, and cutting fixes each.

## A worked walk-through (6 cities, two triangles)

Picture 6 cities in two clusters: {1,2,3} close together and {4,5,6} close together, with the two clusters far apart.

**Step 1 — solve the relaxation (degree = 2 only).** The cheapest way to give everyone exactly two edges is... two separate triangles: 1–2–3–1 and 4–5–6–6. Total is small, but it's **two loops, not one tour**. This is the *subtour* cheat.

**Step 2 — add a subtour-elimination cut.** For the city set `S = {1,2,3}`, any real tour must leave `S` and come back, so it must use **at least 2 edges crossing out of S**:
```
sum of x_e over edges with exactly one endpoint in {1,2,3}  ≥  2
```
The two-triangles answer used **0** crossing edges, so this cut forbids it — but every genuine tour satisfies it. Add it and re-solve.

**Step 3 — re-solve.** Now the LP is forced to connect the clusters. Suppose it returns a connected answer but with some fractional edges — say two edges at `x_e = 0.5` (half-roads), an honest-looking "average of two tours." That's the *fractional* cheat.

**Step 4 — try another cut, else branch.** Look for a known inequality (e.g. a **comb** inequality) that this fractional point violates; if found, add it and re-solve (still no branching). If no useful cut is found, **branch**: pick a fractional edge `e` and make two subproblems — one with `x_e = 1` (force the edge in), one with `x_e = 0` (forbid it). Solve each with more cutting.

**Step 5 — stop with a proof.** Keep going until the LP relaxation's value (lower bound) **equals** the length of an actual integer tour you've found (upper bound). When bound meets tour, that tour is **proven optimal**.

## Why it works

- **Cuts never delete real tours.** Each inequality (like "≥ 2 edges leave S") is something *every* valid tour obeys, so adding it can only remove illegal/fractional points. The set of real tours is untouched — that's why the final answer is still exact.
- **Cuts raise the lower bound.** Every cut chops off the current cheap-but-illegal LP point, forcing the next solve to return something more expensive and more tour-like. The bound climbs toward the truth.
- **Branching handles the leftovers.** When cuts can't separate a stubborn fractional point quickly, splitting on an edge guarantees progress — the same proven-correct mechanism as branch-and-bound.

## Why it's the champion

The relaxation gives a *very* tight lower bound once good cuts (subtour, comb, clique-tree) are added — usually within a percent or two of optimal. A tight bound means **almost no branching is needed**, so the search tree stays tiny even for huge maps. That combination is why branch-and-cut, in the **Concorde** solver, has proven optimal tours through **tens of thousands of cities** — far beyond brute force (~12) or Held–Karp dynamic programming (~20).
