# Nearest neighbor — Level 3: High-school student

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Online / greedy route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; often decent but can be poor
> **Used for:** Building a quick initial tour by always going to the closest unvisited stop
> **Level 3 of 6** — high-school: a worked example with real numbers, numbered steps, and why it works (and fails). See sibling files for other levels.

---

## The problem, concretely

Same 4-city instance as the other artifacts. Start at **A**, visit **B, C, D**, return:

|       | A  | B  | C  | D  |
|-------|----|----|----|----|
| **A** | –  | 10 | 15 | 20 |
| **B** | 10 | –  | 35 | 25 |
| **C** | 15 | 35 | –  | 30 |
| **D** | 20 | 25 | 30 | –  |

Exact optimum is **80** (A→B→D→C→A). Let's see what greedy "always nearest" produces.

## The steps (starting at A)

1. **At A.** Unvisited: B(10), C(15), D(20). Nearest is **B (10)**. Go to B. Tour so far: A→B, length 10.
2. **At B.** Unvisited: C(35), D(25). Nearest is **D (25)**. Go to D. Tour: A→B→D, length 35.
3. **At D.** Unvisited: C(30). Only choice: **C (30)**. Go to C. Tour: A→B→D→C, length 65.
4. **All visited.** Return home: C→A = 15. Final tour: **A→B→D→C→A**, length 65 + 15 = **80**.

On this instance, nearest-neighbor-from-A happens to hit the optimum (80). Lucky — the greedy choices lined up. But the start city matters a lot, so let's try another.

## Same method, different start (start at C)

1. **At C.** Unvisited: A(15), B(35), D(30). Nearest is **A (15)**. Tour: C→A, length 15.
2. **At A.** Unvisited: B(10), D(20). Nearest is **B (10)**. Tour: C→A→B, length 25.
3. **At B.** Unvisited: D(25). Go to **D (25)**. Tour: C→A→B→D, length 50.
4. **Return home:** D→C = 30. Final: **C→A→B→D→C**, length 50 + 30 = **80**.

Also 80 here — but notice step 4 forced the **expensive D→C = 30** edge to close the loop, the single priciest edge in the table. That's the classic nearest-neighbor failure mode showing up: the leftover city (D) is far from home, so the closing jump is costly. On bigger or nastier instances that forced long edge is exactly what pushes the tour well above optimal.

## The numbered recipe

1. Choose a start city; mark it visited; make it "current."
2. Among all **unvisited** cities, find the one nearest to current.
3. Travel there; mark it visited; it becomes current.
4. Repeat 2–3 until none are unvisited.
5. Add the edge from the last city back to the start.

## Why it works — and why it isn't trustworthy

- **Why it often works:** in most layouts, the closest unvisited city really is a sensible next stop, so stringing together nearest hops gives a reasonable route most of the time.
- **Why it can fail badly:** the method is **myopic** — it never considers what its choice does to *future* steps. By greedily taking all the cheap nearby edges early, it can strand a distant city (or a distant "home") for last, forcing one or two very long edges at the end that a planned route would have avoided. There is **no guarantee**: on adversarial layouts the tour can be many times longer than optimal (it grows worse as the number of cities grows).

## A practical trick: try every start

Because the result depends heavily on the starting city, a common upgrade is **repeated nearest neighbor**: run it from *every* possible start city and keep the best tour. With `n` cities that's `n` runs — still fast — and it usually beats a single arbitrary start by a noticeable margin. Even then there's no promise of optimality; it's just a cheap way to dodge the unluckiest starts.
