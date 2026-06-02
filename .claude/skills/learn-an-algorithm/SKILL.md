---
name: learn-an-algorithm
description: Generate layered, level-tagged explanations of an algorithm (ELI5 through researcher/frontier) as Markdown artifacts, tracked in algorithms.json. Use when the user asks to learn/teach/explain an algorithm at one or more levels or descriptors (e.g. "explain nearest neighbor at level 1", "teach me Held-Karp ELI5", "2-opt at levels 1-3"); to pick the next algorithm that has no explanations yet or isn't fully explained; to bulk-generate the next N algorithms at all pending levels; to add a new algorithm to the registry; or to list which algorithms/levels exist.
---

# learn-an-algorithm

Produces progressively deeper explanations of an algorithm, one Markdown file per level, organized one folder per algorithm, with a JSON registry tracking what exists.

## Core conventions

- **Registry**: `algorithms.json` at the project root. Holds level definitions in `meta` and the list of `algorithms`. Read it first, every time.
- **Folder per algorithm**: `algos/{full algorithm name}/` — the folder uses the algorithm's **full human-readable name**, NOT the slug. (If the name contains `/`, render that slash as ` - ` so the folder is filesystem-creatable; flag it in the report.)
- **File per level**: `algos/{full algorithm name}/level{n}-{slug}.md` — only the filename uses the slug.
- **Levels**: 6-rung ladder, defined in `algorithms.json` → `meta.levels`. Descriptor aliases (ELI5, high school, grad, …) map to numbers there.
- **No cascade**: generate ONLY the level requested, never the levels below it.

### Level ladder (authoritative copy in algorithms.json)

| Level | Descriptor | Contains |
|-------|-----------|----------|
| 1 | ELI5 | One analogy, zero jargon, core idea in 1-2 sentences |
| 2 | ELI10 | Analogy + plain walk-through + why we care |
| 3 | High-school | Worked mini-example, numbered steps, intuition |
| 4 | Undergrad CS | Precise statement, pseudocode, Big-O, correctness intuition, mermaid |
| 5 | Graduate | Formal defs, proofs/bounds, paradigm, edge cases, optimizations, mermaid + pseudocode |
| 6 | Researcher | SOTA variants, frontiers, open problems, references, related problems |

### Slugify rule (used ONLY for the .md filename)

1. Lowercase.
2. Strip accents / normalize unicode dashes (é→e, –/—→-).
3. Replace every run of spaces, slashes, or punctuation with a single hyphen.
4. Collapse repeated hyphens; trim leading/trailing hyphens.

Examples: `Christofides–Serdyukov`→`christofides-serdyukov`; `Branch-and-cut (ILP / Concorde-style)`→`branch-and-cut-ilp-concorde-style`; `2-opt local search`→`2-opt-local-search`.

## Workflow: "explain ALGO at LEVEL(S)"

Handles one level OR several at once.

1. **Parse the request** into `(algorithm, levels[], --force?)`. Resolve descriptors (ELI5, high school, grad…) to numbers via `meta.levels[*].aliases`.
   - **Multiple levels** may be given as a list (`levels 1, 3, 5`), a range (`levels 2-4` → 2,3,4), `all` (→ 1-6), or mixed (`1-3 and 5`). Dedupe and sort.
   - If the algorithm or the level(s) are missing/ambiguous, ask before generating.
2. **Read `algorithms.json`.** Match the algorithm:
   - Found → use its stored `name` (folder), `slug` (filename), and metadata.
   - Not found → run the **Add a new algorithm** workflow first, then continue.
3. **For each requested level `n`** (process in ascending order):
   - **Idempotency check.** Path = `algos/{full name}/level{n}-{slug}.md`. If the file exists AND `n` is in `levels_completed` AND no `--force`: **skip it**, note it as already-existing in the report. Otherwise generate (or overwrite on `--force`).
   - **Write the artifact** (template + depth rules below). Create the algorithm folder if needed.
   - Add `n` to that algo's `levels_completed` (sorted, deduped).
4. **Save the registry** once, after all levels processed. Keep JSON valid.
5. **Commit & push** (see Git section) — one commit covering all generated levels for the algo.
6. **Report**: per-level status (written / skipped-exists / regenerated), the paths, and the algo's updated `levels_completed`.

## Selection & bulk commands

"Next" always means **the first matching algorithm in registry array order** (top of `algorithms.algorithms` downward). This makes selection deterministic and march down the list. The full level set is `{1,2,3,4,5,6}`; an algo's *pending* levels are that set minus its `levels_completed`.

### "Pick the next untouched algorithm" (no explanations yet)
Trigger: "next new algorithm", "what should I start next", "next with nothing".
- Find the first algo with `levels_completed == []`.
- **Do NOT generate.** Just announce it (name + type + guarantee + use) and ask which level(s) to explain. Then hand off to the explain-at-levels workflow on the user's reply.
- If none qualify (every algo has ≥1 level), say so and suggest the partial-coverage command below.

### "Pick the next not-fully-explained algorithm"
Trigger: "next incomplete algorithm", "next one missing levels".
- Find the first algo whose `levels_completed` is a strict subset of `{1..6}` (i.e. missing at least one level).
- **Do NOT generate.** Announce it, show which levels are **done** and which are **pending**, and ask which pending level(s) to do. Hand off on reply.
- If every algo is fully explained, congratulate and stop.

### "Bulk: do the next N algorithms at all pending levels"
Trigger: "bulk next 3", "do the next 5 algorithms", "generate next N at all pending levels".
- Parse `N` (and optional `--force`, and optional level filter like "bulk next 3 at levels 1-2").
- Select the first `N` algorithms in registry order that have **pending levels** (skip fully-complete ones unless `--force`, which reselects regardless and regenerates all/filtered levels).
- Confirm scope before a large run: list the selected algorithms and the total number of files to be generated, and proceed once acknowledged (skip confirmation for small N ≤ 2 or if the user said "just do it").
- For each selected algo, run the explain-at-levels workflow over its pending levels (or the filtered subset). Respect idempotency per level.
- Update the registry as you go. **One commit per algorithm** (keeps history readable and a failure mid-run leaves a clean trail), pushed at the end or per-algo.
- Report a summary table: algorithm → levels generated / skipped.

Distinction to keep straight: commands #2 and #3 are **read-only selectors that pause for instructions**; the bulk command is the only selector that **generates**.

## Artifact template

Every artifact starts with this header, then level-appropriate body:

```markdown
# {Algorithm Name} — Level {n}: {Descriptor}

> **Problem:** {problem}  ·  **Type:** {type}  ·  **Guarantee:** {guarantee}
> **Level {n} of 6** — {one-line audience}. See sibling files for other levels.

---

{body}
```

### Depth rules per level

- **L1 (ELI5):** A single vivid analogy. No numbers, no jargon. 1-2 short sentences of "what it does."
- **L2 (ELI10):** The analogy expanded, a plain-language walk-through of the steps, and a "why this is useful." Still no formal notation.
- **L3 (High-school):** A small concrete worked example with real numbers, a numbered step list, and intuition for *why* it works / where it can go wrong.
- **L4 (Undergrad):** Precise problem statement, clean **pseudocode** block, time & space **Big-O**, correctness/termination intuition, and a **mermaid** flowchart of the control flow. Note known guarantees.
- **L5 (Graduate):** Formal definitions, the underlying design paradigm, proof sketches of bounds/approximation ratios, invariants, edge cases, practical optimizations, complexity derivation. Include **mermaid** (flow or state) AND annotated **pseudocode**.
- **L6 (Researcher):** State-of-the-art variants and refinements, theoretical frontiers and lower bounds, open problems, connections to related problems, and a short **references** list (seminal + modern). Cite by author/year; mark anything uncertain.

Use mermaid for L4-L6 (always), optionally lower if it genuinely clarifies. Keep prose tight; prefer worked examples over hand-waving.

## Workflow: add a new algorithm (name-only)

When the user gives just a name (or the requested algorithm isn't in the registry):

1. Compute its `slug` via the slugify rule (for the filename); keep the full `name` for the folder.
2. Infer the remaining fields from knowledge: `type` (Exact / Approximation / Heuristic (constructive|improvement) / Metaheuristic), `guarantee` (optimality or approximation bound, or "No guarantee; …"), `use` (typical use), `problem` (default "TSP" unless stated otherwise).
3. Append the object to `algorithms.algorithms` with `levels_completed: []`.
4. Keep the file valid JSON. Then continue with whatever level was requested.

If a field is genuinely uncertain, state your assumption in the report rather than blocking.

## Workflow: status / listing

On "what algorithms do I have / what levels exist", read `algorithms.json` and summarize each algorithm with its `levels_completed`. Don't open every file — the registry is the source of truth (but if a file is missing for a listed level, flag the drift).

## Git

After writing an artifact and updating the registry:
- Work on `main` is fine for this learning repo (the user pushes to their own repo).
- `git add` the new/changed files (`algos/{full name}/...` and `algorithms.json`), commit with a message like `Add {Algorithm} level {n} ({descriptor})`, and push.
- End commit messages with the Co-Authored-By trailer.
- Only commit the artifact + registry; don't sweep in unrelated changes.

## Guardrails

- Never regenerate an existing level without `--force`. The artifact existing IS the record of completion.
- One fact the registry must always reflect reality: `levels_completed` ⇔ files on disk.
- Keep `algorithms.json` parseable at all times (it's the index the skill depends on).
- Don't cascade levels. One request = one level (unless the user explicitly asks for a range like "levels 1-3").
