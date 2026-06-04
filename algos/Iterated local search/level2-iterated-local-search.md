# Iterated local search — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; layered on local search
> **Used for:** Repeatedly perturbing then re-optimizing a local optimum
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## You've already seen this one — in disguise

Back in the local-search cluster, **chained Lin–Kernighan** did this exact thing: run Lin–Kernighan to a local optimum, give the tour a "double-bridge kick," run Lin–Kernighan again, keep the better. **Iterated local search (ILS) is the general recipe behind that.** Chained Lin–Kernighan is just ILS with two specific choices plugged in (Lin–Kernighan as the local search, double-bridge as the kick). ILS is the simple, universal framework — and it's one of the most effective metaheuristics there is.

## The escape mechanism: perturbation

A local search (like 2-opt) gets **stuck** at a local optimum. ILS's escape idea is **perturbation**: don't restart from scratch (that throws away everything you learned) and don't accept random worse moves (that's simulated annealing) — instead, **kick the current good solution just hard enough to escape its dip, but gently enough to stay nearby**, then run local search again from there.

That "just right" strength is the whole art: too gentle and local search undoes the kick, landing you back where you started; too hard and it's the same as starting over randomly.

## The three ingredients

ILS is built from three plug-in parts:
1. **LocalSearch** — your "roll downhill" routine (2-opt, Or-opt, Lin–Kernighan…).
2. **Perturb** — the kick (for TSP, the classic is the **double bridge**: cut the tour in four places and reconnect them in a swapped order — a change local search can't simply undo).
3. **Accept** — the rule for keeping the new solution: usually "keep it only if it's better," sometimes "keep it if it's not much worse" to wander a little.

## The plain walk-through

1. **Build** a starting solution and run **LocalSearch** → your current best.
2. **Perturb** the current solution (kick it).
3. **LocalSearch** the kicked solution → a new local optimum.
4. **Accept**: if it's better, adopt it; otherwise keep the old one.
5. **Repeat** 2–4 for as long as you have time; return the best ever seen.

## Why it matters

- **Tiny idea, huge payoff.** ILS is barely more than "local search in a loop with a kick," yet in the form of chained Lin–Kernighan it produces some of the **best TSP tours known**, on maps with millions of cities.
- **It searches smartly.** By always kicking a *good* solution (not a random one), it explores the landscape of *good* solutions directly, reusing structure instead of rediscovering it each time.
- **It's a template, not one algorithm.** Swap in any local search and any kick and you have an ILS for scheduling, assignment, routing — anything with a local search and a sensible perturbation.

The honest caveat: **no guarantee**, and it inherits all of local search's needs *plus* two design choices — the **perturbation strength** and the **acceptance rule** — both of which matter a lot. Get the kick wrong and ILS degrades into either plain local search (too gentle) or random restart (too hard). It's the **perturbation-based** escape; its single-solution siblings escape by **memory** (tabu search), **neighborhood-switching** (variable neighborhood search), and **randomized restart** (GRASP).

