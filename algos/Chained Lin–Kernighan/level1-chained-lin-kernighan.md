# Chained Lin–Kernighan — Level 1: ELI5

> **Example problems:** Traveling Salesman Problem, Large-scale Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; often extremely close to optimal
> **Used for:** Restarting Lin–Kernighan from perturbed tours for large-scale, very-high-quality solving
> **Level 1 of 6** — explain-like-I'm-5: one analogy, no jargon. See sibling files for other levels.

---

You used the clever untangling trick until the loop was as neat as that trick could make it — but it's *stuck*: it can't get any better, even though a slightly different loop would be shorter.

So you do something sneaky: you give the loop one quick, deliberate **shake** — a special little scramble that the untangling trick can't undo by itself — and then you **run the untangling trick again** from the shaken-up loop. If the result is shorter, you keep it; if not, you go back.

Shake, untangle, keep-the-better — over and over. Each shake jiggles the loop out of being stuck, and the untangling tidies it back up, a little better each time. That's how you get loops that are **as close to perfect as anyone knows how to make.**

