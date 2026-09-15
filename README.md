# Algorithms Learning

Layered explanations of algorithms, each written at six depths: from a one-analogy ELI5 up to researcher-level notes on state-of-the-art variants and open problems. The core set is **22 algorithms for the Traveling Salesman Problem (TSP)**, from exact solvers through metaheuristics. There are also **3 ensembles** that trace game-playing AI from simple greedy play to multi-agent reinforcement learning.

That makes 150 Markdown files: 25 topics × 6 levels.

## The six levels

| Level | Descriptor | What it contains |
|---|---|---|
| 1 | ELI5 | One analogy, zero jargon, the core idea in 1–2 sentences |
| 2 | ELI10 | Analogy + plain walk-through + why it matters |
| 3 | High-school | A worked mini-example with real numbers, numbered steps, intuition |
| 4 | Undergraduate CS | Precise statement, pseudocode, Big-O, correctness intuition, Mermaid flowchart |
| 5 | Graduate | Formal definitions, proofs of bounds, design paradigm, edge cases, optimizations |
| 6 | Researcher / frontier | SOTA variants, lower bounds, open problems, related problems, references |

Every file opens with the same header: example problems, type, guarantee, what the algorithm is used for, and which level it is. Levels 4–6 include Mermaid diagrams, which GitHub renders inline.

## How to read it

- **Go deep on one algorithm:** start at its lowest level you find easy and move up.
- **Survey the field:** read every algorithm at a single level (for example, all Level 3 files) in table order.

The TSP tables follow a deliberate progression. **Exact** methods are optimal but exponential. **Approximation** algorithms trade optimality for a proven bound. **Constructive** heuristics build a starting tour fast. **Improvement** heuristics repair that tour. **Metaheuristics** wrap local search to escape local optima. Later files often refer back to earlier ones.

## Traveling Salesman Problem

"Worst-case guarantee" means the tour's cost relative to the optimal tour (OPT). "Metric" means the distances satisfy the triangle inequality; the bounds don't hold without it.

### Exact algorithms

| Algorithm | Worst-case guarantee | Levels |
|---|---|---|
| [Brute force enumeration](algos/Brute%20force%20enumeration/) | Optimal · Θ(n!) time | [1](algos/Brute%20force%20enumeration/level1-brute-force-enumeration.md) · [2](algos/Brute%20force%20enumeration/level2-brute-force-enumeration.md) · [3](algos/Brute%20force%20enumeration/level3-brute-force-enumeration.md) · [4](algos/Brute%20force%20enumeration/level4-brute-force-enumeration.md) · [5](algos/Brute%20force%20enumeration/level5-brute-force-enumeration.md) · [6](algos/Brute%20force%20enumeration/level6-brute-force-enumeration.md) |
| [Held–Karp dynamic programming](algos/Held%E2%80%93Karp%20dynamic%20programming/) | Optimal · O(n²·2ⁿ) time, O(n·2ⁿ) space | [1](algos/Held%E2%80%93Karp%20dynamic%20programming/level1-held-karp-dynamic-programming.md) · [2](algos/Held%E2%80%93Karp%20dynamic%20programming/level2-held-karp-dynamic-programming.md) · [3](algos/Held%E2%80%93Karp%20dynamic%20programming/level3-held-karp-dynamic-programming.md) · [4](algos/Held%E2%80%93Karp%20dynamic%20programming/level4-held-karp-dynamic-programming.md) · [5](algos/Held%E2%80%93Karp%20dynamic%20programming/level5-held-karp-dynamic-programming.md) · [6](algos/Held%E2%80%93Karp%20dynamic%20programming/level6-held-karp-dynamic-programming.md) |
| [Branch-and-bound](algos/Branch-and-bound/) | Optimal · exponential worst case | [1](algos/Branch-and-bound/level1-branch-and-bound.md) · [2](algos/Branch-and-bound/level2-branch-and-bound.md) · [3](algos/Branch-and-bound/level3-branch-and-bound.md) · [4](algos/Branch-and-bound/level4-branch-and-bound.md) · [5](algos/Branch-and-bound/level5-branch-and-bound.md) · [6](algos/Branch-and-bound/level6-branch-and-bound.md) |
| [Branch-and-cut (ILP / Concorde-style)](algos/Branch-and-cut%20%28ILP%20-%20Concorde-style%29/) | Optimal · exponential worst case, fastest exact method in practice | [1](algos/Branch-and-cut%20%28ILP%20-%20Concorde-style%29/level1-branch-and-cut-ilp-concorde-style.md) · [2](algos/Branch-and-cut%20%28ILP%20-%20Concorde-style%29/level2-branch-and-cut-ilp-concorde-style.md) · [3](algos/Branch-and-cut%20%28ILP%20-%20Concorde-style%29/level3-branch-and-cut-ilp-concorde-style.md) · [4](algos/Branch-and-cut%20%28ILP%20-%20Concorde-style%29/level4-branch-and-cut-ilp-concorde-style.md) · [5](algos/Branch-and-cut%20%28ILP%20-%20Concorde-style%29/level5-branch-and-cut-ilp-concorde-style.md) · [6](algos/Branch-and-cut%20%28ILP%20-%20Concorde-style%29/level6-branch-and-cut-ilp-concorde-style.md) |

### Approximation algorithms

| Algorithm | Worst-case guarantee | Levels |
|---|---|---|
| [MST doubling (tree-doubling)](algos/MST%20doubling%20%28tree-doubling%29/) | ≤ 2 · OPT (metric) | [1](algos/MST%20doubling%20%28tree-doubling%29/level1-mst-doubling-tree-doubling.md) · [2](algos/MST%20doubling%20%28tree-doubling%29/level2-mst-doubling-tree-doubling.md) · [3](algos/MST%20doubling%20%28tree-doubling%29/level3-mst-doubling-tree-doubling.md) · [4](algos/MST%20doubling%20%28tree-doubling%29/level4-mst-doubling-tree-doubling.md) · [5](algos/MST%20doubling%20%28tree-doubling%29/level5-mst-doubling-tree-doubling.md) · [6](algos/MST%20doubling%20%28tree-doubling%29/level6-mst-doubling-tree-doubling.md) |
| [Christofides–Serdyukov](algos/Christofides%E2%80%93Serdyukov/) | ≤ 1.5 · OPT (metric) | [1](algos/Christofides%E2%80%93Serdyukov/level1-christofides-serdyukov.md) · [2](algos/Christofides%E2%80%93Serdyukov/level2-christofides-serdyukov.md) · [3](algos/Christofides%E2%80%93Serdyukov/level3-christofides-serdyukov.md) · [4](algos/Christofides%E2%80%93Serdyukov/level4-christofides-serdyukov.md) · [5](algos/Christofides%E2%80%93Serdyukov/level5-christofides-serdyukov.md) · [6](algos/Christofides%E2%80%93Serdyukov/level6-christofides-serdyukov.md) |

### Constructive heuristics

| Algorithm | Worst-case guarantee | Levels |
|---|---|---|
| [Nearest neighbor](algos/Nearest%20neighbor/) | Θ(log n) · OPT (metric) | [1](algos/Nearest%20neighbor/level1-nearest-neighbor.md) · [2](algos/Nearest%20neighbor/level2-nearest-neighbor.md) · [3](algos/Nearest%20neighbor/level3-nearest-neighbor.md) · [4](algos/Nearest%20neighbor/level4-nearest-neighbor.md) · [5](algos/Nearest%20neighbor/level5-nearest-neighbor.md) · [6](algos/Nearest%20neighbor/level6-nearest-neighbor.md) |
| [Greedy edge-selection (cheapest link)](algos/Greedy%20edge-selection%20%28cheapest%20link%29/) | O(log n) · OPT (metric); no constant bound | [1](algos/Greedy%20edge-selection%20%28cheapest%20link%29/level1-greedy-edge-selection-cheapest-link.md) · [2](algos/Greedy%20edge-selection%20%28cheapest%20link%29/level2-greedy-edge-selection-cheapest-link.md) · [3](algos/Greedy%20edge-selection%20%28cheapest%20link%29/level3-greedy-edge-selection-cheapest-link.md) · [4](algos/Greedy%20edge-selection%20%28cheapest%20link%29/level4-greedy-edge-selection-cheapest-link.md) · [5](algos/Greedy%20edge-selection%20%28cheapest%20link%29/level5-greedy-edge-selection-cheapest-link.md) · [6](algos/Greedy%20edge-selection%20%28cheapest%20link%29/level6-greedy-edge-selection-cheapest-link.md) |
| [Nearest insertion](algos/Nearest%20insertion/) | ≤ 2 · OPT (metric) | [1](algos/Nearest%20insertion/level1-nearest-insertion.md) · [2](algos/Nearest%20insertion/level2-nearest-insertion.md) · [3](algos/Nearest%20insertion/level3-nearest-insertion.md) · [4](algos/Nearest%20insertion/level4-nearest-insertion.md) · [5](algos/Nearest%20insertion/level5-nearest-insertion.md) · [6](algos/Nearest%20insertion/level6-nearest-insertion.md) |
| [Cheapest insertion](algos/Cheapest%20insertion/) | ≤ 2 · OPT (metric) | [1](algos/Cheapest%20insertion/level1-cheapest-insertion.md) · [2](algos/Cheapest%20insertion/level2-cheapest-insertion.md) · [3](algos/Cheapest%20insertion/level3-cheapest-insertion.md) · [4](algos/Cheapest%20insertion/level4-cheapest-insertion.md) · [5](algos/Cheapest%20insertion/level5-cheapest-insertion.md) · [6](algos/Cheapest%20insertion/level6-cheapest-insertion.md) |
| [Random / farthest insertion](algos/Random%20-%20farthest%20insertion/) | O(log n) · OPT (metric); no constant bound proven | [1](algos/Random%20-%20farthest%20insertion/level1-random-farthest-insertion.md) · [2](algos/Random%20-%20farthest%20insertion/level2-random-farthest-insertion.md) · [3](algos/Random%20-%20farthest%20insertion/level3-random-farthest-insertion.md) · [4](algos/Random%20-%20farthest%20insertion/level4-random-farthest-insertion.md) · [5](algos/Random%20-%20farthest%20insertion/level5-random-farthest-insertion.md) · [6](algos/Random%20-%20farthest%20insertion/level6-random-farthest-insertion.md) |

### Improvement heuristics (local search)

| Algorithm | Worst-case guarantee | Levels |
|---|---|---|
| [2-opt local search](algos/2-opt%20local%20search/) | No constant bound; large practical gains | [1](algos/2-opt%20local%20search/level1-2-opt-local-search.md) · [2](algos/2-opt%20local%20search/level2-2-opt-local-search.md) · [3](algos/2-opt%20local%20search/level3-2-opt-local-search.md) · [4](algos/2-opt%20local%20search/level4-2-opt-local-search.md) · [5](algos/2-opt%20local%20search/level5-2-opt-local-search.md) · [6](algos/2-opt%20local%20search/level6-2-opt-local-search.md) |
| [3-opt local search](algos/3-opt%20local%20search/) | No constant bound; better than 2-opt, slower | [1](algos/3-opt%20local%20search/level1-3-opt-local-search.md) · [2](algos/3-opt%20local%20search/level2-3-opt-local-search.md) · [3](algos/3-opt%20local%20search/level3-3-opt-local-search.md) · [4](algos/3-opt%20local%20search/level4-3-opt-local-search.md) · [5](algos/3-opt%20local%20search/level5-3-opt-local-search.md) · [6](algos/3-opt%20local%20search/level6-3-opt-local-search.md) |
| [Lin–Kernighan heuristic](algos/Lin%E2%80%93Kernighan%20heuristic/) | No bound; near-optimal in practice | [1](algos/Lin%E2%80%93Kernighan%20heuristic/level1-lin-kernighan-heuristic.md) · [2](algos/Lin%E2%80%93Kernighan%20heuristic/level2-lin-kernighan-heuristic.md) · [3](algos/Lin%E2%80%93Kernighan%20heuristic/level3-lin-kernighan-heuristic.md) · [4](algos/Lin%E2%80%93Kernighan%20heuristic/level4-lin-kernighan-heuristic.md) · [5](algos/Lin%E2%80%93Kernighan%20heuristic/level5-lin-kernighan-heuristic.md) · [6](algos/Lin%E2%80%93Kernighan%20heuristic/level6-lin-kernighan-heuristic.md) |
| [Chained Lin–Kernighan](algos/Chained%20Lin%E2%80%93Kernighan/) | No bound; very close to optimal in practice | [1](algos/Chained%20Lin%E2%80%93Kernighan/level1-chained-lin-kernighan.md) · [2](algos/Chained%20Lin%E2%80%93Kernighan/level2-chained-lin-kernighan.md) · [3](algos/Chained%20Lin%E2%80%93Kernighan/level3-chained-lin-kernighan.md) · [4](algos/Chained%20Lin%E2%80%93Kernighan/level4-chained-lin-kernighan.md) · [5](algos/Chained%20Lin%E2%80%93Kernighan/level5-chained-lin-kernighan.md) · [6](algos/Chained%20Lin%E2%80%93Kernighan/level6-chained-lin-kernighan.md) |

### Metaheuristics

| Algorithm | Worst-case guarantee | Levels |
|---|---|---|
| [Simulated annealing](algos/Simulated%20annealing/) | None (stochastic) | [1](algos/Simulated%20annealing/level1-simulated-annealing.md) · [2](algos/Simulated%20annealing/level2-simulated-annealing.md) · [3](algos/Simulated%20annealing/level3-simulated-annealing.md) · [4](algos/Simulated%20annealing/level4-simulated-annealing.md) · [5](algos/Simulated%20annealing/level5-simulated-annealing.md) · [6](algos/Simulated%20annealing/level6-simulated-annealing.md) |
| [Genetic algorithm / evolutionary algorithm](algos/Genetic%20algorithm%20-%20evolutionary%20algorithm/) | None (stochastic) | [1](algos/Genetic%20algorithm%20-%20evolutionary%20algorithm/level1-genetic-algorithm-evolutionary-algorithm.md) · [2](algos/Genetic%20algorithm%20-%20evolutionary%20algorithm/level2-genetic-algorithm-evolutionary-algorithm.md) · [3](algos/Genetic%20algorithm%20-%20evolutionary%20algorithm/level3-genetic-algorithm-evolutionary-algorithm.md) · [4](algos/Genetic%20algorithm%20-%20evolutionary%20algorithm/level4-genetic-algorithm-evolutionary-algorithm.md) · [5](algos/Genetic%20algorithm%20-%20evolutionary%20algorithm/level5-genetic-algorithm-evolutionary-algorithm.md) · [6](algos/Genetic%20algorithm%20-%20evolutionary%20algorithm/level6-genetic-algorithm-evolutionary-algorithm.md) |
| [Ant colony optimization](algos/Ant%20colony%20optimization/) | None (stochastic) | [1](algos/Ant%20colony%20optimization/level1-ant-colony-optimization.md) · [2](algos/Ant%20colony%20optimization/level2-ant-colony-optimization.md) · [3](algos/Ant%20colony%20optimization/level3-ant-colony-optimization.md) · [4](algos/Ant%20colony%20optimization/level4-ant-colony-optimization.md) · [5](algos/Ant%20colony%20optimization/level5-ant-colony-optimization.md) · [6](algos/Ant%20colony%20optimization/level6-ant-colony-optimization.md) |
| [Tabu search](algos/Tabu%20search/) | None | [1](algos/Tabu%20search/level1-tabu-search.md) · [2](algos/Tabu%20search/level2-tabu-search.md) · [3](algos/Tabu%20search/level3-tabu-search.md) · [4](algos/Tabu%20search/level4-tabu-search.md) · [5](algos/Tabu%20search/level5-tabu-search.md) · [6](algos/Tabu%20search/level6-tabu-search.md) |
| [Iterated local search](algos/Iterated%20local%20search/) | None | [1](algos/Iterated%20local%20search/level1-iterated-local-search.md) · [2](algos/Iterated%20local%20search/level2-iterated-local-search.md) · [3](algos/Iterated%20local%20search/level3-iterated-local-search.md) · [4](algos/Iterated%20local%20search/level4-iterated-local-search.md) · [5](algos/Iterated%20local%20search/level5-iterated-local-search.md) · [6](algos/Iterated%20local%20search/level6-iterated-local-search.md) |
| [Variable neighborhood search](algos/Variable%20neighborhood%20search/) | None | [1](algos/Variable%20neighborhood%20search/level1-variable-neighborhood-search.md) · [2](algos/Variable%20neighborhood%20search/level2-variable-neighborhood-search.md) · [3](algos/Variable%20neighborhood%20search/level3-variable-neighborhood-search.md) · [4](algos/Variable%20neighborhood%20search/level4-variable-neighborhood-search.md) · [5](algos/Variable%20neighborhood%20search/level5-variable-neighborhood-search.md) · [6](algos/Variable%20neighborhood%20search/level6-variable-neighborhood-search.md) |
| [GRASP](algos/GRASP/) | None (randomized multi-start) | [1](algos/GRASP/level1-grasp.md) · [2](algos/GRASP/level2-grasp.md) · [3](algos/GRASP/level3-grasp.md) · [4](algos/GRASP/level4-grasp.md) · [5](algos/GRASP/level5-grasp.md) · [6](algos/GRASP/level6-grasp.md) |

## Game-AI ensembles

An ensemble combines several algorithms into one topic. Each level covers the *whole* combination at that depth. Level 1 isn't limited to the first technique and Level 6 to the last; every level tells the full story.

| Ensemble | Example domains · progression | Levels |
|---|---|---|
| [Adversarial game-tree search - minimax to AlphaZero](ensemble/Adversarial%20game-tree%20search%20-%20minimax%20to%20AlphaZero/) | Chess, Go · greedy eval → minimax → alpha-beta → quiescence → learned eval (TD) → MCTS → AlphaZero self-play | [1](ensemble/Adversarial%20game-tree%20search%20-%20minimax%20to%20AlphaZero/level1-adversarial-game-tree-search-minimax-to-alphazero.md) · [2](ensemble/Adversarial%20game-tree%20search%20-%20minimax%20to%20AlphaZero/level2-adversarial-game-tree-search-minimax-to-alphazero.md) · [3](ensemble/Adversarial%20game-tree%20search%20-%20minimax%20to%20AlphaZero/level3-adversarial-game-tree-search-minimax-to-alphazero.md) · [4](ensemble/Adversarial%20game-tree%20search%20-%20minimax%20to%20AlphaZero/level4-adversarial-game-tree-search-minimax-to-alphazero.md) · [5](ensemble/Adversarial%20game-tree%20search%20-%20minimax%20to%20AlphaZero/level5-adversarial-game-tree-search-minimax-to-alphazero.md) · [6](ensemble/Adversarial%20game-tree%20search%20-%20minimax%20to%20AlphaZero/level6-adversarial-game-tree-search-minimax-to-alphazero.md) |
| [Survival games - expectimax to multi-agent RL](ensemble/Survival%20games%20-%20expectimax%20to%20multi-agent%20RL/) | Battle royale, poker · expectimax → belief states (POMDP) → CFR → multi-agent RL self-play, risk-sensitive objectives, opponent modeling | [1](ensemble/Survival%20games%20-%20expectimax%20to%20multi-agent%20RL/level1-survival-games-expectimax-to-multi-agent-rl.md) · [2](ensemble/Survival%20games%20-%20expectimax%20to%20multi-agent%20RL/level2-survival-games-expectimax-to-multi-agent-rl.md) · [3](ensemble/Survival%20games%20-%20expectimax%20to%20multi-agent%20RL/level3-survival-games-expectimax-to-multi-agent-rl.md) · [4](ensemble/Survival%20games%20-%20expectimax%20to%20multi-agent%20RL/level4-survival-games-expectimax-to-multi-agent-rl.md) · [5](ensemble/Survival%20games%20-%20expectimax%20to%20multi-agent%20RL/level5-survival-games-expectimax-to-multi-agent-rl.md) · [6](ensemble/Survival%20games%20-%20expectimax%20to%20multi-agent%20RL/level6-survival-games-expectimax-to-multi-agent-rl.md) |
| [Team objective FPS - cooperative–competitive multi-agent RL](ensemble/Team%20objective%20FPS%20-%20cooperative%E2%80%93competitive%20multi-agent%20RL/) | Team shooters, Capture the Flag, Dota 2 · team Markov games → Dec-POMDPs → centralized training / decentralized execution → value decomposition (VDN, QMIX, COMA) → league self-play | [1](ensemble/Team%20objective%20FPS%20-%20cooperative%E2%80%93competitive%20multi-agent%20RL/level1-team-objective-fps-cooperative-competitive-multi-agent-rl.md) · [2](ensemble/Team%20objective%20FPS%20-%20cooperative%E2%80%93competitive%20multi-agent%20RL/level2-team-objective-fps-cooperative-competitive-multi-agent-rl.md) · [3](ensemble/Team%20objective%20FPS%20-%20cooperative%E2%80%93competitive%20multi-agent%20RL/level3-team-objective-fps-cooperative-competitive-multi-agent-rl.md) · [4](ensemble/Team%20objective%20FPS%20-%20cooperative%E2%80%93competitive%20multi-agent%20RL/level4-team-objective-fps-cooperative-competitive-multi-agent-rl.md) · [5](ensemble/Team%20objective%20FPS%20-%20cooperative%E2%80%93competitive%20multi-agent%20RL/level5-team-objective-fps-cooperative-competitive-multi-agent-rl.md) · [6](ensemble/Team%20objective%20FPS%20-%20cooperative%E2%80%93competitive%20multi-agent%20RL/level6-team-objective-fps-cooperative-competitive-multi-agent-rl.md) |

## Repository layout

```
algos/                      standalone algorithms (one folder each)
  Nearest neighbor/
    level1-nearest-neighbor.md
    ...
    level6-nearest-neighbor.md
ensemble/                   multi-algorithm ensembles (same structure)
```

- **Folder:** the algorithm's full name. Characters that aren't safe in file paths (`/ \ : * ? " < > |`) become ` - `, so `Random / farthest insertion` lives in `Random - farthest insertion/`.
- **File:** `level{n}-{slug}.md`. The slug is the name in lowercase with accents stripped and each run of punctuation replaced by one hyphen, e.g. `Christofides–Serdyukov` → `christofides-serdyukov`.

## How the content was produced

The files were generated with Claude Code, using a local skill (`learn-an-algorithm`) and a JSON registry (`algorithms.json`) that tracks which levels exist for each topic. Both are local tooling and are gitignored, so neither is in this repository.
