# Held–Karp dynamic programming — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Shortest Hamiltonian path, Sequential ordering / scheduling with setup costs  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour; time O(n^2·2^n)
> **Used for:** Exact solving and benchmarking for small–medium instances via subset DP
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you have to visit a bunch of friends' houses and come home, the shortest way possible. Instead of trying *every* whole route from scratch, you do something clever: you remember the **best way to reach a friend after visiting a certain group of friends**, and write it on a sticky note. Then whenever you need that answer again, you just read the sticky note instead of figuring it out all over.

By saving and reusing these little answers, you find the perfect shortest trip with **way less** work than trying every route one by one.
