# GRASP — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Set covering, Scheduling  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; multi-start greedy + local search
> **Used for:** Multi-start randomized-greedy construction followed by local search
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

Optimum is **80** (A–B–D–C–A). GRASP does **construct → local-search**, many times over, keeping the best. The new idea to see is the **Restricted Candidate List (RCL)** — a randomized version of the greedy "nearest city" rule.

## The RCL: greedy, but not *too* greedy

Building a tour by always hopping to the nearest unvisited city is the **nearest-neighbor** heuristic — but it makes the *same* tour every time. GRASP randomizes it: at each step, list the candidate cities, then keep only the ones whose distance is within a threshold

```
threshold = min + α · (max − min)
```

(the **RCL**), and pick one **at random** from it. The dial `α` runs from `0` (pure greedy: RCL = just the nearest) to `1` (pure random: RCL = everything). Use `α = 0.5` here.

## Iteration 1 — construct

Start at A.
- **From A:** candidates B(10), C(15), D(20). `min=10, max=20`, threshold `= 10 + 0.5·10 = 15`. RCL = cities within 15 = **{B(10), C(15)}**. Randomly pick — say **C**.
- **From C:** unvisited B(35), D(30). `min=30, max=35`, threshold `= 30 + 0.5·5 = 32.5`. RCL = **{D(30)}** (B=35 is above). Pick **D**.
- **From D:** only **B(25)** left. Then back to A.
- **Constructed tour:** A–C–D–B–A = `15+30+25+10 = 80`.

**Local search (2-opt):** already 80 — nothing to improve. Best so far = **80**.

## Iteration 2 — construct (different random luck)

Start at A.
- **From A:** RCL **{B(10), C(15)}** again — this time pick **B**.
- **From B:** C(35), D(25). threshold `= 25 + 0.5·10 = 30`. RCL = **{D(25)}**. Pick **D**.
- **From D:** **C(30)**. Back to A.
- **Constructed tour:** A–B–D–C–A = **80** (the optimum directly).

**Local search:** already optimal. Best so far = **80**.

> On this tiny instance the RCL is small and both restarts land on 80, so it just shows the *mechanism*. On a 1000-city map, the RCL randomness makes every restart build a genuinely **different** tour; each is then 2-opt-polished, and you keep the best of hundreds of independent tries.

## The numbered recipe

1. **Repeat** for many iterations:
   1. **Construct** a tour: at each step build the RCL = candidates within `min + α·(max−min)`, pick one **at random**, add it; repeat until the tour is complete.
   2. **Local search** (2-opt) the constructed tour to a local optimum.
   3. **Update best-so-far.**
2. Return the best tour.

## Why it works

- **Randomized greed = diversity with quality.** Pure greedy gives one fixed (often mediocre) tour; the RCL keeps choices *good* while making each restart *different*, so many restarts explore many regions — all worth polishing.
- **Construction + local search split the work.** Construction lands you in a good neighborhood; local search cleans up the details. Each is simple; together they're strong.
- **Independent restarts can't get permanently stuck.** If one construction leads to a bad local optimum, the next restart begins somewhere completely different — no single trap holds the whole search.

## The catch

**No guarantee**, and the dial `α` matters: too greedy (`α≈0`) and every restart is nearly the same tour; too random (`α≈1`) and you're polishing junk. The big structural weakness: restarts are **independent**, so GRASP **forgets** good tours between rounds — it doesn't reuse them (like iterated local search) or learn from them (like ant colony). The standard fix, **path relinking**, adds memory by exploring tours "between" good past solutions. On the plain TSP, Lin–Kernighan-style methods usually beat GRASP; its appeal is **simplicity, parallelism, and generality**. GRASP is the **randomized-restart** escape — the last of the single-solution metaheuristics (with **tabu search**, **iterated local search**, and **variable neighborhood search**).

