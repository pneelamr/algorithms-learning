# Brute force enumeration — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem (tiny), small permutation / sequencing problems  ·  **Type:** Exact  ·  **Guarantee:** Optimal tour for very small n
> **Used for:** Teaching and tiny instances; ground-truth baseline to check other methods
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

Say you want to visit your three best friends — Ana, Ben, and Cara — then come back home. You start and end at your own house. The question is: in what *order* should you visit them so the total walking distance is as short as possible?

You could guess. But brute force enumeration doesn't guess — it's stubborn and thorough. It writes down **every possible order** you could visit your friends, measures the total distance of each one, and circles the shortest. Because it literally tried them all, you *know* nothing shorter exists. There's no "maybe I missed a better route" — it checked every route there is.

## The plain walk-through

Here's what the method does, step by step:

1. **Fix a starting point.** You always begin and end at home, so home is locked in. You only need to decide the order of the *friends in between*.
2. **List every possible order of the friends.** With Ana, Ben, and Cara that's: ABC, ACB, BAC, BCA, CAB, CBA — six orders in total.
3. **Turn each order into a full loop.** For example ABC becomes: Home → Ana → Ben → Cara → Home.
4. **Add up the distance of each loop.** Measure each leg and total it.
5. **Keep the smallest total.** Whichever loop has the shortest total distance is the winner — and it's guaranteed to be the best one.

That's the whole idea: *list all routes, measure each, pick the minimum.*

## Why the loop count explodes

The catch is in step 2. The number of orders grows shockingly fast as you add friends:

- 3 friends → 6 orders
- 4 friends → 24 orders
- 5 friends → 120 orders
- 10 friends → over 3.6 **million** orders
- 15 friends → over a **trillion** orders

Each extra friend multiplies the work by how many friends you have. This is why brute force only works for a tiny number of stops — beyond about 10 or 12, even a fast computer would be busy for ages.

## Why we care anyway

If it's so slow, why learn it? Two reasons:

- **It's always right.** For small problems, brute force gives the provably best answer — perfect for checking whether faster, cleverer methods are actually finding good routes.
- **It defines the problem.** Seeing *why* trying everything blows up so fast is exactly what motivates all the smarter algorithms that come later (the ones that find great routes without checking every single one).

Brute force is the honest baseline: simple, correct, and hopelessly slow — which is the perfect setup for everything that improves on it.
