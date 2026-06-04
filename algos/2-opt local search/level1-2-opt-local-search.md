# 2-opt local search — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Job scheduling  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; big practical improvement
> **Used for:** Improving a tour by uncrossing pairs of edges until no swap helps
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you already drew a loop connecting all your friends' houses, but you were sloppy and two parts of the loop **cross over each other like an X**. That crossing is a waste — the string has to stretch extra far to make the X.

2-opt finds an X, **snips the two crossing strings, and ties them back the straight way** so they don't cross anymore. Every time you uncross an X, the loop gets a little shorter. Keep hunting for X's and straightening them until there are none left — now your loop is neat and tight.

The big idea: you don't build a new loop, you **take a loop you already have and tidy it up.**

