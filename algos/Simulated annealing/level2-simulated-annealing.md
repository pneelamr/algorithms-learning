# Simulated annealing — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, VLSI circuit placement, Job-shop scheduling, Continuous function optimization  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Escaping local minima by accepting worse moves with cooling probability
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The question this answers

All the local-search methods (2-opt, 3-opt, Lin–Kernighan) only ever take steps that make the tour **shorter**. That's why they get **stuck** in a "local optimum" — a tour where every small change makes things worse, even though a better tour exists if you could just go uphill for a moment. Chained Lin–Kernighan escaped by *kicking* the tour. **Simulated annealing escapes a different way: it sometimes accepts a worse move on purpose** — and that's the start of a whole new family called **metaheuristics**.

## The analogy, expanded

The name comes from **annealing** metal: heat it up so its atoms jiggle freely, then cool it **slowly** so they settle into a strong, low-energy crystal. Cool too fast and you get a brittle, flawed lump (stuck in a bad arrangement).

Simulated annealing copies this with a "**temperature**" dial:
- **Hot (early):** accept almost any change, even ones that make the tour longer — you roam freely and escape bad valleys.
- **Cooling (middle):** become pickier; accept *small* worsenings sometimes, big ones rarely.
- **Cold (end):** only accept improvements — you settle down and polish.

The exact rule for accepting a worse move: the **bigger** the worsening, the **less** likely you accept it; and the **hotter** the temperature, the **more** likely you accept it. As temperature drops toward zero, accepting worse moves fades away.

## The plain walk-through

1. **Start** with any tour and a **high temperature**.
2. **Propose a small change** (e.g., a 2-opt swap — reverse a segment).
3. **If it's shorter, take it.** If it's longer, take it anyway **with a probability** that's high when it's hot and when the damage is small.
4. **Cool down** a little (lower the temperature).
5. **Repeat** thousands of times, keeping track of the best tour you ever saw. Stop when it's cold.

## Why it matters

- **A general escape hatch.** The "accept worse moves, less often over time" idea isn't just for tours — it solves chip layout, scheduling, and tuning continuous functions. It's one of the most famous **general-purpose** optimization methods.
- **Simple and broadly applicable.** You only need: a way to make a small change, a way to score a solution, and a cooling schedule. No deep problem-specific cleverness required.
- **It has a theoretical safety net.** If you cool *infinitely slowly*, it's guaranteed to find the best answer (not practical, but reassuring — it says the idea is sound).

The honest caveat: it has **no real-world guarantee** (you can't cool infinitely slowly), and its success leans heavily on the **cooling schedule** — cool too fast and it gets stuck like plain local search; too slow and it wastes enormous time. On the TSP specifically, a well-tuned Lin–Kernighan usually beats simulated annealing — but SA's *generality* is the point. It's the first of several "how to escape a local optimum" strategies; its siblings (**tabu search**, **genetic algorithms**, **ant colony**, **GRASP**, **VNS**) each answer the same question differently.

