# Genetic algorithm / evolutionary algorithm — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Job scheduling, Knapsack / feature selection, Hyperparameter / architecture search  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; stochastic
> **Used for:** Population-based global search via selection, crossover, and mutation
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you're breeding the fastest puppies. You start with a whole **bunch** of puppies, not just one. You let the **fastest ones** be the parents, **mix** two parents together to make babies that take a bit from each, and every now and then a baby is born a little **different by accident**. Do this for many generations and the puppies get faster and faster.

A genetic algorithm breeds **answers** the same way. It keeps a *crowd* of candidate answers, lets the **best** ones be "parents," **mixes** two good answers into a new one, occasionally **tweaks** an answer at random, and repeats — so the crowd gets better and better over time, with the best ever found kept safe.

