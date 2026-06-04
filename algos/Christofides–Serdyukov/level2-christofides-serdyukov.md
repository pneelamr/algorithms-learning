# Christofides–Serdyukov — Level 2: ELI10

> **Example problems:** Metric Traveling Salesman Problem, Vehicle Routing Problem, Logistics / delivery route planning  ·  **Type:** Approximation  ·  **Guarantee:** At most 1.5× optimal (metric TSP)
> **Used for:** Balanced quality/speed approximation with a worst-case bound
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

This algorithm is a smarter cousin of MST doubling. Both start the same way: build the **minimum spanning tree (MST)** — the cheapest web of roads connecting every city, no loops. And both want to turn that tree into a single round trip.

The problem with a tree is *parity*. To walk one continuous loop that traces roads and comes home, **every city needs an even number of road-ends** touching it. In a tree, some cities are dead-ends or junctions with an *odd* number — and those are what stop you from making a clean loop.

MST doubling fixes this with a sledgehammer: copy **every** road, so every count doubles and becomes even. Christofides is surgical instead. It notices that only *some* cities have an odd count, and it just **pairs those odd cities up** with a few cheap extra roads — leaving everything else alone. Far less added length, same even-everywhere result.

## The key facts that make it click

- **The number of odd cities is always even.** (Road-ends always come in pairs across the whole tree, so the odd-count cities can always be matched two-by-two — none left over.)
- **Pairing them cheaply is a known, solvable puzzle** called *minimum-weight perfect matching*: connect the odd cities in pairs so the total added distance is as small as possible.

## The plain walk-through

1. **Build the MST.** Cheapest connected web of roads, no cycles.
2. **Find the odd cities** — the ones touched by an odd number of tree roads.
3. **Cheaply pair them up.** Add the minimum-cost set of roads that matches every odd city to exactly one other odd city.
4. **Now every city is even.** Tree + pairing roads together let you walk one big closed loop (an "Euler tour") that uses every one of those roads.
5. **Walk it and take shortcuts.** List cities in the order you first reach them; skip any you've already seen, hopping straight to the next new one. That gives a clean tour visiting each city once.

## Why it matters

- **A better promise: 1.5× instead of 2×.** Because the cheap pairing costs at most *half* the optimal tour (a neat fact proven at higher levels), the final tour is guaranteed to be **at most one-and-a-half times the best possible** — a real improvement over MST doubling's 2×.
- **It held the record for 45 years.** From 1976 until 2021, 1.5× was the *best known* guarantee for metric TSP that anyone could prove. Only a tiny, impractical improvement has beaten it since.
- **It's practical.** Fast enough for real route planning, and a great starting tour to hand to local-search polishers (like 2-opt) that squeeze it down further.

The one assumption, same as MST doubling: distances must be "**metric**" — a direct hop is never longer than a detour (true for real maps). That's what makes the shortcuts safe.

Christofides is the lesson that *where* you spend effort matters: patch only the broken spots (odd cities), not the whole structure, and you get a provably better result for less added cost.
