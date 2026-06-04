# Cheapest insertion — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Logistics route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No formal bound in general
> **Used for:** Growing a tour by always making the globally cheapest insertion
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

You have a little loop of string connecting some friends' houses, and a pile of friends still waiting to join.

Instead of asking *"who is closest to the loop?"*, cheapest insertion asks the bigger question: **"out of *every* waiting friend and *every* spot in the loop, which single tie-in stretches the string the least?"** It looks at all the choices at once and does the one cheapest tie-in.

Then it asks again, and again — each time picking the **one move, anywhere, that grows the loop the smallest amount** — until everyone is on the loop.

