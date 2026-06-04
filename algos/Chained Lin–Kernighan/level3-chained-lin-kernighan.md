# Chained Lin–Kernighan — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Large-scale Vehicle Routing Problem  ·  **Type:** Heuristic (improvement)  ·  **Guarantee:** No formal bound; often extremely close to optimal
> **Used for:** Restarting Lin–Kernighan from perturbed tours for large-scale, very-high-quality solving
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works. See sibling files for other levels.

---

## The setup

Lin–Kernighan (LK) polishes a tour until it's *locally* optimal — stuck. Chained LK escapes by **kicking** the stuck tour with a **double bridge** and running LK again. Let's see one full cycle with numbers.

## The double bridge, concretely

Take a tour written as a sequence of cities, split into four consecutive chunks **A · B · C · D**:

```
tour  =  [A][B][C][D]      (then back to the start of A)
kick  =  [A][C][B][D]      (swap the middle two chunks)
```

Example on 8 cities. Current LK-optimal tour (your **best**), length **100**:

```
best:  1 2 | 3 4 | 5 6 | 7 8       → back to 1
        A      B     C     D
```

The double bridge cuts the four edges `2–3`, `4–5`, `6–7`, `8–1` and reconnects as **A · C · B · D**:

```
kick:  1 2 | 5 6 | 3 4 | 7 8       → back to 1
        A      C     B     D
```

New edges added: `2–5`, `6–3`, `4–7`, `8–1`. Suppose this kick **lengthens** the tour from 100 to **115** — that's fine and expected; a kick is supposed to disturb, not improve.

## Re-optimize and keep the better

Now run **LK** on the kicked tour (length 115). LK does its chain-of-swaps tidying and suppose it settles at a new local optimum of length **96**.

- `96 < 100` → **improvement!** Adopt the length-96 tour as the new **best**.

If instead LK had come back with, say, **103**, we'd **reject** it and keep the old best (100), then kick the old best again. Either way we never lose ground.

## One more cycle

Kick the new best (96) → some scrambled tour (say 110) → LK → maybe **94**. `94 < 96`, keep it. Repeat. Each round either improves the best or is discarded, so the best only ever goes **down**, slowly grinding toward the true optimum.

## The numbered recipe (iterated local search)

1. **Build** a starting tour and run **LK** → call it `best`.
2. **Kick:** apply a **double bridge** to `best` → a perturbed tour.
3. **Re-optimize:** run **LK** on the perturbed tour → `candidate`.
4. **Accept:** if `candidate` is shorter than `best`, set `best ← candidate`. (Otherwise keep `best`.)
5. **Repeat** 2–4 for as long as you have time.

## Why it works

- **The kick is just right.** A double bridge is small (it keeps all four chunks whole, only re-orders them), so LK starts from *near* a good tour and can re-tidy fast — but it's also a move **LK can't make or undo on its own**, so it genuinely lands you in a new place instead of being instantly reversed. Too gentle a kick and LK just walks back; too violent and you've thrown away all your good structure (like restarting from scratch). The double bridge is the famous "just right" perturbation.
- **Keeping the better never loses.** Because you only adopt improvements, the best tour is a ratchet — it can't get worse, only better or stay put.
- **Many cheap tries beat one big search.** Each cycle is one LK run, which is fast; doing thousands of them explores far more of the landscape than any single descent could.

## The catch

It needs the (cleverly coded) LK engine inside it, and it has **no guarantee** — it's "merely" the best thing we know in practice, often landing **well under 1%** from optimal. There are also choices that matter: sometimes you accept a slightly *worse* tour occasionally to keep exploring, and how you pick the four cut points affects results. Those "how to escape a local optimum" choices are exactly what the **next family — metaheuristics** (simulated annealing, tabu search, genetic algorithms) — explores in different ways.

