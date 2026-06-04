# Tabu search — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Job-shop scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; avoids cycling
> **Used for:** Local search with memory of recent moves to avoid revisiting solutions
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

Optimum is **80** (A–B–D–C–A). Tabu search is 2-opt **plus memory**: it always makes a move (even a worsening one when stuck), and it keeps a short list of recently-used moves it isn't allowed to undo.

## The setup

- **Moves:** 2-opt swaps (delete two edges, reconnect the other way — reversing a segment).
- **Tabu list:** the edges we just *added* are "frozen" for the next **2 steps** — a move that would remove a frozen edge is forbidden (tabu).
- **Aspiration:** a tabu move is allowed anyway if it would beat the best tour ever seen.
- We always remember the **best tour so far**.

## The trace

**Step 0 — start at A–B–C–D–A (length 95).** Best-so-far = 95.

**Step 1.** Look at all 2-opt neighbors. The best one removes edges B–C and D–A, adds B–D and C–A → **A–B–D–C–A (80)**. It's improving, so take it. **Freeze the added edges B–D and C–A** (tabu for 2 steps). Best-so-far = **80**.

**Step 2 — now at 80 (the optimum).** *Every* 2-opt move here makes the tour **worse** (back to a 95). Plain 2-opt would **stop here, stuck.** Tabu search keeps going: it takes the **least-bad** move. But the move that would slide straight back to where we came from needs to remove a **frozen** edge (B–D or C–A) — that's **tabu**, so it's blocked. The search is forced to take a *different* worsening move, to the *other* 95-tour, say **A–C–B–D–A (95)**. (We don't lose anything: best-so-far is still safely 80.)

**Step 3 — at 95 again.** The improving move back to the optimum **A–B–D–C–A (80)** would normally be... let's check: is it tabu? Even if part of it were frozen, it would reach **80 = our best-ever**, so the **aspiration criterion overrides the tabu** and we take it. Back to **80**, confirmed as the best.

> On a tiny 4-city map there are only three tours, so this just shows the *mechanism*: the tabu list **stopped the search from instantly bouncing back** into the local optimum, forcing it to explore, while aspiration let it grab the record when offered. On a 1000-city map this is exactly what lets tabu search wander out of a deep-but-not-best valley and find a better one — without ever cycling.

## The numbered recipe

1. **Start** with a tour; empty tabu list; record best-so-far.
2. **Generate all neighbor moves** (e.g. 2-opt swaps).
3. **Choose the best move that is not tabu** — even if it worsens the tour — **unless** a tabu move would beat best-so-far (**aspiration** → allow it).
4. **Add the move's attribute** (e.g. the edges it created) to the tabu list with a **tenure** (how many steps it stays frozen); drop expired entries.
5. **Update best-so-far.** Repeat 2–4 for many steps; return best-so-far.

## Why it works

- **It always moves, so it never freezes.** By taking the least-bad move when nothing improves, it climbs out of local optima that stop plain 2-opt.
- **Memory blocks the bounce-back.** Forbidding the undo of recent moves stops the search cycling between the same two or three solutions — the failure mode that "always move" would otherwise cause.
- **Aspiration keeps it greedy when it counts.** The one exception — "take a forbidden move if it sets a new record" — means the rules never cost you a genuinely great solution.

## The catch

**No guarantee**, and the behavior depends on tuning: the **tabu tenure** (too short → cycles return; too long → too many good moves blocked), *what* you forbid (a full solution, a move, or just an attribute like an edge), and the aspiration rules. On the plain TSP, Lin–Kernighan-style methods usually beat it; tabu search shines most on **scheduling and assignment** problems. It's the **memory-based** escape; next up are **iterated local search** (escape by perturbation), **variable neighborhood search** (escape by switching neighborhoods), and **GRASP** (escape by randomized restarts).

