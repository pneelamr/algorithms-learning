# Genetic algorithm / evolutionary algorithm — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Job scheduling, Knapsack / feature selection, Hyperparameter / architecture search  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Population-based global search via selection, crossover, and mutation
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The problem, concretely

Same 4-city instance as the other artifacts:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Optimum is **80** (A–B–D–C–A). A GA doesn't build or tweak *one* tour — it evolves a **population** of tours. Two new ideas to see: **selection** (fitter tours breed more) and **crossover** (mix two parents).

## Part 1 — Selection, on the shared instance

There are only three distinct tours here. Score each by length, and set **fitness = 1 / length** (shorter = fitter):

| Tour | Length | Fitness `1/len` | Selection share |
|------|--------|------------------|-----------------|
| A–B–D–C–A | **80** | 0.01250 | **37%** |
| A–B–C–D–A | 95 | 0.01053 | 31% |
| A–C–B–D–A | 95 | 0.01053 | 31% |

"Selection share" = each fitness divided by the total (0.03356). So if we spin a roulette wheel weighted by fitness, the **best tour (80) is picked ~37%** of the time vs ~31% for each worse tour. That's **selection pressure**: better tours become parents more often, without ever fully banning the others (keeping variety). Over generations this pushes the population toward the 80 tour.

## Part 2 — Crossover, on a 6-city sketch

Four cities is too short for crossover to be interesting (the tours barely differ), so here's the *mechanism* on 6 cities `1..6`, using **Order Crossover (OX)** — a standard TSP crossover that always yields a valid tour.

```
Parent 1:  1 2 [3 4] 5 6        ← pick a window, say positions 3–4
Parent 2:  4 3  2 1  6 5
```

1. **Copy Parent 1's window** straight into the child: `_ _ [3 4] _ _`.
2. **Fill the rest from Parent 2's order**, skipping cities already taken (3 and 4). Reading Parent 2 → 4,3,2,1,6,5 → drop 3,4 → leftover order **2, 1, 6, 5**.
3. Drop those into the child's empty slots: child = **`2 1 3 4 6 5`**.

The child **inherited the middle segment (3–4) from Parent 1** and the **relative order of the rest from Parent 2** — a genuine blend, and still a legal tour (each city once).

## Part 3 — Mutation

Occasionally tweak a child for fresh variety. A common TSP mutation is **inversion** (reverse a stretch — exactly a 2-opt move):

```
before:  2 1 3 4 6 5
reverse positions 3–5:  2 1 [6 4 3] 5   →  2 1 6 4 3 5
```

Mutation rate is kept **low** (a few percent) — enough to inject novelty, not so much that it destroys good structure.

## The numbered recipe

1. **Initialize** a random population of valid tours.
2. **Evaluate** fitness (e.g. `1/length`) for each.
3. **Select** parents, favoring fitter tours (roulette wheel or "pick the best of a random few").
4. **Crossover** parent pairs (OX/PMX/edge-based) → valid children.
5. **Mutate** some children a little (swap / inversion).
6. **Replace** the population with the children, usually **keeping the best few unchanged (elitism)**.
7. **Repeat** for many generations; return the best tour ever seen.

## Why it works

- **A crowd explores many regions at once.** Unlike a single wandering solution, a population covers different parts of the search space in parallel, so it's harder to get *all* of it stuck.
- **Crossover assembles good pieces.** If one parent has a great left half and another a great right half, crossover can produce a child with both — recombination is the power single-solution methods lack.
- **Selection + mutation balance focus and variety.** Selection pulls the crowd toward good tours; mutation keeps enough diversity that it doesn't collapse onto one mediocre answer too early.

## The catch

GAs have **no guarantee** and **many knobs** (population size, mutation rate, which crossover, selection method) that must be tuned — easy to get wrong. On the TSP a plain GA usually **loses to Lin–Kernighan**. The fix that makes GAs genuinely competitive is the **memetic** version: run a local search (like 2-opt or Lin–Kernighan) on every child, so the population evolves over *already-polished* tours. That hybrid — population search + local polishing — is where this method shines, and it links straight back to the local-search cluster.

