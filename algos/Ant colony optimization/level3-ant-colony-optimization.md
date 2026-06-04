# Ant colony optimization — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Network routing  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; often good on structured instances
> **Used for:** Pheromone-guided construction inspired by ant foraging
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

Optimum is **80** (A–B–D–C–A). In ACO, "ants" build tours guided by two numbers on each edge: **pheromone** `τ` (learned: how good has this edge been?) and a fixed **heuristic** `η = 1/distance` (common sense: short edges are attractive).

## Step 1 — one ant builds a tour

Start every edge with the **same pheromone** `τ = 1`. An ant at a city picks its next city with probability proportional to `τ^α · η^β`. Use `α = 1`, `β = 2` (standard) — so with all `τ = 1` the choice is driven by `η² = (1/distance)²`.

**Ant starts at A.** The candidates and their weights `η²`:

| Next city | distance | `η = 1/dist` | weight `η²` | probability |
|-----------|----------|--------------|-------------|-------------|
| B | 10 | 0.100 | 0.0100 | **59%** |
| C | 15 | 0.067 | 0.0044 | 26% |
| D | 20 | 0.050 | 0.0025 | 15% |

The ant most likely heads to **B** (59%) — short edges are favored, but it *could* still pick C or D (that's the exploration). Say it picks **B**.

**At B**, unvisited {C, D}: weights `η²` are `(1/35)² ≈ 0.0008` for C and `(1/25)² = 0.0016` for D → P(D) ≈ 66%. Say it picks **D**. **At D**, only **C** is left, then back to **A**.

**Ant's tour: A→B→D→C→A = 10+25+30+15 = 80** — the optimum. (A second ant, exploring differently, might instead build a 95-length tour like A–C–B–D–A. Both happen; that's the colony covering options.)

## Step 2 — update the pheromone

After all ants finish, two things happen to **every** edge:

1. **Evaporate:** `τ ← (1 − ρ)·τ`. With evaporation rate `ρ = 0.5`, every `τ` halves (1 → 0.5). This lets old, stale trails fade.
2. **Deposit:** each ant adds `Q/L` to the edges it used, where `L` is its tour length (`Q = 100` here). **Shorter tours deposit more** (`100/80 = 1.25` beats `100/95 ≈ 1.05`).

Suppose Ant 1 built the 80-tour (edges A-B, B-D, D-C, C-A) and Ant 2 built the 95-tour A–C–B–D–A (edges A-C, C-B, B-D, D-A). After evaporation (every edge → 0.5) plus deposits:

| Edge | in optimal tour? | new pheromone `τ` |
|------|------------------|--------------------|
| A–C | yes (and used by both ants) | **2.80** (highest) |
| B–D | yes (used by both ants) | **2.80** (highest) |
| A–B | yes | 1.75 |
| C–D | yes | 1.75 |
| A–D | no | 1.55 (lowest) |
| **B–C** | no (the long 35 edge) | 1.55 (lowest) |

The edges that appear in good tours now carry **more** pheromone; the edges that don't (especially the long B–C) carry the **least**. Next round, ants are biased toward the strong edges — and the colony tightens around the 80-tour.

## The numbered recipe

1. **Initialize** equal pheromone on all edges.
2. **Each ant builds a tour**, choosing the next city with probability ∝ `τ^α · η^β` (`η = 1/distance`).
3. **Score** each tour by length.
4. **Evaporate** all pheromone: `τ ← (1−ρ)·τ`.
5. **Deposit** `Q/L` on each ant's edges (shorter tours deposit more; strong variants let only the *best* ant deposit).
6. **Repeat** for many rounds; keep the best tour ever seen.

## Why it works

- **Two kinds of knowledge combine.** `η = 1/distance` is instant common sense (prefer near cities); pheromone `τ` is **learned experience** (this edge keeps showing up in good tours). The product `τ^α·η^β` blends them.
- **Good edges reinforce, bad edges fade.** Shorter tours deposit more, and evaporation erases stale trails — a feedback loop that concentrates probability on edges common to good tours.
- **The colony is a shared memory.** No single ant is smart; the *pheromone map* accumulates the colony's collective discovery, getting better each round.

## The catch

ACO has **no guarantee** and several **knobs** — number of ants, `α` vs `β` (how much to trust learning vs. distance), evaporation `ρ`, deposit amount — that strongly affect results and need tuning. Too little evaporation and it **freezes early** on a mediocre tour; too much and it forgets and wanders. On the plain TSP it usually **loses to Lin–Kernighan**. The competitive versions add a **2-opt or Lin–Kernighan polish** to every ant's tour — the same hybrid trick the genetic algorithm used. ACO is the *constructive, learning* member of the metaheuristic family; **tabu search, GRASP,** and **VNS** are the single-solution members still to come.

