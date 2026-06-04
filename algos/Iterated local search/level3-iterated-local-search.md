# Iterated local search — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Scheduling, Quadratic assignment  ·  **Type:** Metaheuristic  ·  **Guarantee:** No guarantee; layered on local search
> **Used for:** Repeatedly perturbing then re-optimizing a local optimum
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The setup

Iterated local search (ILS) loops three steps: **local search → perturb → local search**, keeping the better. We'll use **2-opt** as the local search and the **double-bridge** kick as the perturbation. (Swap in Lin–Kernighan and this becomes *chained Lin–Kernighan* from the local-search cluster.)

## The double-bridge kick

Write a tour as four consecutive chunks **A · B · C · D** and reconnect them in the order **A · C · B · D** (swap the middle two):

```
tour  =  [A][B][C][D]
kick  =  [A][C][B][D]
```

This cuts 4 edges and is special because **2-opt (and even Lin–Kernighan) can't undo it in a single move** — so the kick genuinely moves you to a new area, but keeps all four chunks intact so you stay nearby.

## A worked cycle (8 cities)

Start with an 8-city tour that 2-opt has already polished to a **local optimum of length 100** — but suppose the true best is shorter and 2-opt is stuck.

**Step 1 — perturb.** Split into four chunks and double-bridge them:

```
current (100):  1 2 | 3 4 | 5 6 | 7 8      → back to 1
kicked:         1 2 | 5 6 | 3 4 | 7 8      → back to 1
```

The kick cuts edges 2–3, 4–5, 6–7, 8–1 and adds 2–5, 6–3, 4–7, 8–1. Suppose this **worsens** the tour to length **118** — expected; a kick disturbs.

**Step 2 — local search.** Run 2-opt on the kicked tour (118). It uncrosses and tidies, settling at a new local optimum of length **94**.

**Step 3 — accept.** `94 < 100` → **better!** Adopt the length-94 tour as the new current/best.

**Next cycle.** Kick the 94-tour → 2-opt → maybe **97**. `97 > 94` → **reject**, keep 94, and kick the 94-tour again. The best only ever improves or holds — a ratchet.

> Why not the shared 4-city instance? With only 4 cities there's no "deep but not best" valley for 2-opt to get stuck in (every tour is one swap from the optimum 80), so a kick has nothing to escape. ILS's power only appears when local search *can* get stuck — which needs a bigger map.

## The numbered recipe

1. **Build** a starting tour; run **local search** (2-opt) → current = best.
2. **Perturb:** apply a double-bridge kick to current.
3. **Local search** the kicked tour → a new local optimum.
4. **Accept:** if it beats current, adopt it; else keep current. Update best-so-far.
5. **Repeat** 2–4 for the time budget; return best.

## Why it works

- **Kicking a *good* solution beats restarting.** The kick keeps most of the current tour's good structure, so local search re-optimizes from a strong starting point — far more efficient than building a fresh random tour each time.
- **The double bridge is the "just right" kick.** Big enough that local search won't immediately reverse it (escape), small enough that you stay in a good region (exploit). Too small → you fall back to the same optimum; too big → it's just random restart.
- **Keep-the-better never loses.** Because you only adopt improvements (or near-ties), the best solution can't get worse over time.

## The catch

**No guarantee**, and two choices decide everything: the **perturbation strength** (how many/how big the kicks) and the **acceptance rule** (strictly-better, or occasionally accept slightly worse to wander). Get them wrong and ILS collapses into plain local search or random restart. Done right — especially as **chained Lin–Kernighan** — it's among the best TSP methods known. ILS is the **perturbation-based** escape; its siblings are **tabu search** (memory), **variable neighborhood search** (switch neighborhoods), and **GRASP** (randomized restart).

