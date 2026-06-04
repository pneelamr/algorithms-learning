# Branch-and-cut (ILP / Concorde-style) — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Mixed-integer linear programs, Vehicle Routing Problem, Max-cut  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour; state-of-the-art exact performance
> **Used for:** Research-grade exact solving by combining branching with cutting planes
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

Imagine you're finding the shortest way to visit every house and come home. You make a quick guess, but it cheats — it draws little separate loops instead of one big trip. So you add a **rule** that says "no little loops allowed!" and try again. Each time the answer cheats in a new way, you add a new rule to forbid exactly that cheat, and the answer gets closer and closer to a real, honest trip.

That's branch-and-cut: it keeps **slicing away** the cheating answers with new rules (the *cuts*), and when slicing isn't enough it **splits** the problem into cases (the *branch*). Together they find the perfect shortest trip — even for thousands of houses.
