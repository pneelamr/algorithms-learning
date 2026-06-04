# Genetic algorithm / evolutionary algorithm — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Job scheduling, Knapsack / feature selection, Hyperparameter / architecture search  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Population-based global search via selection, crossover, and mutation
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## A new kind of escape

Simulated annealing kept **one** solution and wandered, occasionally accepting a worse move, to escape getting stuck. A genetic algorithm (GA) escapes a completely different way: it keeps a **whole population** of solutions at once and **combines** them. Two mediocre tours might each have one *good half*; mixing them can produce a tour with *both* good halves — something no single-solution method can do. This is the first **population-based** metaheuristic in this cluster.

## The analogy, expanded — borrowing from evolution

GAs copy natural selection. The vocabulary:
- **Individual / chromosome:** one candidate solution. For the TSP, a chromosome is a **tour** (an order to visit the cities).
- **Fitness:** how good it is. For TSP, **shorter tour = higher fitness**.
- **Population:** a crowd of tours, kept all together.
- **Selection:** pick parents, favoring the fitter ones (good tours breed more).
- **Crossover (recombination):** combine two parent tours into a child that inherits pieces of each.
- **Mutation:** randomly tweak a child a little (e.g., swap two cities) to add fresh variety.

Repeat selection → crossover → mutation for many **generations**, and the population drifts toward shorter and shorter tours.

## The plain walk-through

1. **Start** with a random population of tours.
2. **Score** each tour by length (shorter = fitter).
3. **Select** parents, preferring fitter tours.
4. **Cross over** pairs of parents to make children that mix both.
5. **Mutate** some children slightly for variety.
6. **Replace** the old population with the new generation (often keeping the best few unchanged — "elitism" — so you never lose your best).
7. **Repeat** 2–6 for many generations; return the best tour ever seen.

## The TSP twist (why GAs need special care here)

You can't just chop two tours in half and glue them — you'd get a "tour" that visits some cities twice and others never. So GAs for the TSP use **clever crossovers** designed to always produce a *valid* tour (visiting every city exactly once). The best ones focus on inheriting **edges** (which city follows which) rather than positions, because for the TSP it's the edges that determine length.

## Why it matters

- **Combining beats wandering — sometimes.** Mixing good partial solutions ("building blocks") can assemble high-quality answers that single-trajectory methods (SA, plain local search) can't reach as directly.
- **Wildly general.** The same selection/crossover/mutation recipe solves scheduling, knapsack/feature-selection, and even **hyperparameter and neural-architecture search** — anywhere you can score a candidate and combine two of them.
- **The winning version for TSP is a hybrid.** Plain GAs are mediocre on the TSP; the strong ones — **memetic algorithms** — run a **local search (like Lin–Kernighan) on every child**, marrying this cluster's population idea to the previous cluster's polishing power. The state-of-the-art GA for TSP (using a special "edge-assembly" crossover) rivals the best Lin–Kernighan solvers on huge maps.

The honest caveat: GAs have **no guarantee**, lots of **knobs** (population size, mutation rate, which crossover, how to select), and are easy to tune badly. A plain GA usually loses to Lin–Kernighan on the TSP — it earns its keep through **generality** and through the **hybrid** memetic form. It's the population-based answer to "how do you escape a local optimum?"; **ant colony optimization** is another population-based answer, and **tabu search**, **GRASP**, and **VNS** are single-solution answers.

