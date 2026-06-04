# Ant colony optimization — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Network routing  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; often good on structured instances
> **Used for:** Pheromone-guided construction inspired by ant foraging
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## Another population idea — but built, not bred

Genetic algorithms kept a population of full tours and **mixed** them. Ant colony optimization (ACO) also uses a population — many "ants" — but instead of mixing finished tours, each ant **builds a tour from scratch**, step by step, guided by a shared memory called **pheromone**. So this is the *constructive* population-based metaheuristic: the colony's shared trail-map is what carries learning from one round to the next.

## The analogy, expanded

Real ants leave smell (pheromone) on trails; short trails get reinforced, long ones fade. ACO turns this into numbers:
- Every **edge** between two cities has a **pheromone level** `τ` — "how good has using this edge turned out before?"
- Each edge also has a fixed **heuristic** hint `η` — usually `1/distance`, i.e. "short edges look attractive."
- An ant building a tour, standing at a city, picks the next city **randomly but biased** toward edges with **high pheromone × high attractiveness**. So it mostly follows good, short, well-trodden edges, but sometimes explores.

After all ants finish their tours:
- **Evaporation:** every edge's pheromone fades a bit (so old mistakes don't haunt forever).
- **Deposit:** ants reinforce the edges they used — and ants with **shorter tours deposit more**. Often only the best tour(s) get to reinforce.

Round after round, edges that keep showing up in short tours accumulate pheromone, and the colony converges on an excellent tour.

## The plain walk-through

1. **Start** with equal pheromone on every edge.
2. **Each ant builds a tour:** repeatedly step to a next city, chosen with probability tilted toward high `pheromone × (1/distance)`.
3. **Score** each ant's tour by length.
4. **Evaporate** a fraction of pheromone everywhere.
5. **Deposit** pheromone on the edges of good tours (more for shorter tours).
6. **Repeat** for many rounds; keep the best tour ever found.

## Why it matters

- **Learning where the good edges are.** Unlike a single wanderer (simulated annealing) or even crossover (genetic algorithms), ACO builds a **shared map of which connections tend to be in good solutions** — a kind of collective memory that gets smarter each round.
- **Two forces, nicely balanced.** The **heuristic** `1/distance` gives instant common sense ("prefer near cities"); the **pheromone** adds learned experience ("this edge keeps paying off"). Mixing greedy sense with learned memory is the core trick.
- **Great for routing.** ACO shines on network and vehicle routing, and its online cousin (ants reacting to live traffic) inspired real **network-routing** schemes.

The honest caveat: ACO has **no guarantee**, lots of **knobs** (number of ants, evaporation rate, how strongly to weight pheromone vs. distance), and on the plain TSP it usually **loses to Lin–Kernighan**. The versions that compete add a **local-search polish** (2-opt or Lin–Kernighan) to every ant's tour — the same memetic trick the genetic algorithms used. ACO is the *constructive, learning* answer to "how do you escape a local optimum?"; **tabu search**, **GRASP**, and **VNS** are single-solution answers, and **simulated annealing** and **genetic algorithms** are the other escape styles you've already seen.

