# Nearest neighbor — Level 2: ELI10

> **Example problems:** Traveling Salesman Problem, Vehicle Routing Problem, Online / greedy route construction  ·  **Type:** Heuristic (constructive)  ·  **Guarantee:** No guarantee; often decent but can be poor
> **Used for:** Building a quick initial tour by always going to the closest unvisited stop
> **Level 2 of 6** — for a curious beginner: the analogy expanded, a plain walk-through, and why it matters. See sibling files for other levels.

---

## The analogy, expanded

You want to visit every city once and come home. Nearest neighbor is the most natural strategy a person would invent: **"always go wherever's closest that I haven't been yet."** Stand at your start, look at all the cities you haven't visited, hop to the nearest, and repeat. No planning, no looking ahead — just grab the closest each time.

This is a **greedy** method: it makes the choice that looks best *right now* (the shortest next step), trusting that good local choices add up to a good whole trip. Sometimes they do. Sometimes they don't.

## The plain walk-through

1. **Pick a starting city.** Call it home.
2. **Look at all unvisited cities.** From where you currently stand, find the closest one.
3. **Go there.** Mark it visited; it's your new current city.
4. **Repeat** steps 2–3 until every city is visited.
5. **Return home** from the last city to close the loop.

That's the entire method. It builds the tour one city at a time, each step a quick "who's nearest?" lookup.

## Where it shines and where it stumbles

- **Shines:** it's *fast* and dead simple. For a quick first guess — or as a starting tour you'll polish later — it's hard to beat for effort.
- **Stumbles:** because it never looks ahead, it tends to scoop up all the easy nearby cities first and **strand a far-off one for the end**. Closing the loop back from that stranded city (or back home) can be a long, expensive jump that ruins an otherwise tidy route. People call these leftover long edges the "regret" of being greedy.

A quick mental picture: dots clustered on the left and one lone dot far to the right. Nearest neighbor happily zig-zags through the left cluster, then is *forced* to make one big trek to the lone dot and another big trek back — two expensive edges a smarter route would have planned around.

## Why it matters

- **It's the baseline everyone starts with.** Nearest neighbor is the simplest constructive heuristic, the thing you reach for first and compare everything else against.
- **It's a great seed.** Its rough tour is the perfect *starting point* for improvement methods (like 2-opt or Lin–Kernighan) that un-cross and shorten edges afterward. Build fast with nearest neighbor, then polish.
- **It fits "online" problems.** When cities arrive one at a time and you must decide on the spot (a delivery driver getting new stops live), "go to the nearest" is often the only practical move.

The honest takeaway: nearest neighbor trades quality for speed and simplicity. It comes with **no promise** about how good the tour is — on bad layouts it can be much worse than optimal — but as a fast first draft, it's everywhere.
