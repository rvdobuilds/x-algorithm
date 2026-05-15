# New Repo Transfer Plan

A concrete recommendation for which files in this repository should be copied into the new standalone Next.js app, **X Reach Lab**.

The new repo is a local-first product. It does **not** try to run any part of X's production algorithm. It uses files from this repository only as:

1. Strategy and rubric documents the app reads and renders.
2. Source-of-truth reference files the app cites and that future AI prompts (Claude / ChatGPT) are grounded against.
3. License and attribution material required by Apache 2.0.

Anything that does not serve those three purposes stays out.

---

## File categories used below

| Category            | What it means                                                                                  |
|---------------------|------------------------------------------------------------------------------------------------|
| **strategy**        | A markdown / CSV file authored for X Reach Lab. The app reads or renders it.                  |
| **source reference** | An original repo file, kept verbatim, that strategy docs cite or that grounds AI prompts.    |
| **license / attribution** | Required for redistribution of Apache 2.0 content from this repo.                       |
| **runtime**         | Code the app actually executes. *No file in this plan is runtime.*                             |

---

## 1. Strategy files to copy (already authored for X Reach Lab)

All from `docs/x-reach-lab/` in this repo. These are the product. Copy them all.

| Source path (this repo)                              | Target path (new repo)                          | Category | Reason                                                                                                  |
|------------------------------------------------------|--------------------------------------------------|----------|---------------------------------------------------------------------------------------------------------|
| `docs/x-reach-lab/README.md`                         | `docs/README.md`                                 | strategy | Toolkit overview, weekly routine, scoring flow. The app's "about" content.                              |
| `docs/x-reach-lab/algorithm-insights.md`             | `docs/algorithm-insights.md`                     | strategy | Plain-English reading of the repo. Grounds rules 1–6 in the decision rules. Cited everywhere.            |
| `docs/x-reach-lab/content-rubric.md`                 | `docs/content-rubric.md`                         | strategy | The 1–5 rubric and reach-score formula. The core of the analyzer feature.                                |
| `docs/x-reach-lab/my-positioning.md`                 | `docs/my-positioning.md`                         | strategy | Account promise, pillars, topics to avoid. Powers off-niche detection.                                   |
| `docs/x-reach-lab/post-analysis-template.md`         | `docs/post-analysis-template.md`                 | strategy | Per-draft analysis form. The app fills this in for a post.                                               |
| `docs/x-reach-lab/post-log-template.csv`             | `docs/post-log-template.csv`                     | strategy | CSV schema for the manual performance log feature.                                                       |
| `docs/x-reach-lab/posts.md`                          | `docs/posts.md`                                  | strategy | Posting cadence, timing windows, format catalogue. Used by the posting-planner feature.                  |
| `docs/x-reach-lab/profile.md`                        | `docs/profile.md`                                | strategy | Bio / banner / pinned recommendations. Used by the profile-positioning feature.                          |
| `docs/x-reach-lab/replies.md`                        | `docs/replies.md`                                | strategy | Reply strategy and kill-list. Used by the reply-coach feature.                                           |

Move `docs/x-reach-lab/` to `docs/` in the new repo. The `x-reach-lab` prefix exists here only to namespace inside this repository; in the new repo the whole project is X Reach Lab, so the prefix is noise.

---

## 2. Source reference files to copy

These are from this repo. Copied **verbatim, never modified**, into a single `reference/x-algorithm/` directory in the new repo. They exist so the strategy docs can cite them with a path the app can actually open, and so future AI prompts can be grounded in real code instead of paraphrase.

### Decision: yes, copy

| Source path (this repo)                  | Target path (new repo)                                 | Category         | Decision rules served | Reason                                                                                                                                                                |
|------------------------------------------|--------------------------------------------------------|------------------|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `README.md`                              | `reference/x-algorithm/README.md`                      | source reference | 1, 2, 3, 4, 7, 8       | The top-level explanation of retrieval, ranking, in-network vs out-of-network, the scorer list, and filters. The single most-cited file across the strategy docs.       |
| `phoenix/README.md`                      | `reference/x-algorithm/phoenix-README.md`              | source reference | 1, 5, 7, 8             | Defines the two-tower retrieval, the ranking transformer, candidate isolation, and the sports-corpus example. Grounds the "niche clarity" and "audience fit" rubric.    |
| `phoenix/run_pipeline.py`                | `reference/x-algorithm/phoenix-run_pipeline.py`        | source reference | 1, 3, 5, 7             | Contains the illustrative weighted-score formula and the `RecsysBatch` shape. Cited in `algorithm-insights.md` for retrieval-then-rank composition.                    |
| `home-mixer/scorers/weighted_scorer.rs`  | `reference/x-algorithm/weighted_scorer.rs`             | source reference | 3, 6, 7, 8             | 92 lines. The literal list of positive and negative predicted actions, plus the offset rule for negative scores. Cited by `content-rubric.md`, `replies.md`, `posts.md`. |
| `home-mixer/scorers/oon_scorer.rs`       | `reference/x-algorithm/oon_scorer.rs`                  | source reference | 2, 7                   | 38 lines. The OON adjustment hook. Cited in `algorithm-insights.md §2` as the place where out-of-network candidates are scored separately.                              |
| `home-mixer/filters/vf_filter.rs`        | `reference/x-algorithm/vf_filter.rs`                   | source reference | 4, 8                   | 30 lines. The visibility filter for deleted / spam / violence / gore. Cited in the negative-signal rubric dimension and the profile checklist.                          |
| `home-mixer/filters/age_filter.rs`       | `reference/x-algorithm/age_filter.rs`                  | source reference | 4, 7                   | 36 lines. Hard recency cutoff. Cited in `algorithm-insights.md §6` for the freshness argument.                                                                          |
| `home-mixer/filters/muted_keyword_filter.rs` | `reference/x-algorithm/muted_keyword_filter.rs`    | source reference | 4, 7                   | 57 lines. Cited in `algorithm-insights.md §4` for the "filters drop content before ranking runs" point.                                                                 |
| `LICENSE`                                | `reference/x-algorithm/LICENSE`                        | license / attribution | —                | Apache 2.0. Required when copying any source file from this repo.                                                                                                       |

### Decision: no, do not copy

| Source path (this repo)                                       | Decision | Reason                                                                                                                                                                                              |
|---------------------------------------------------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `candidate-pipeline/candidate_pipeline.rs`                    | exclude  | 493 lines of generic pipeline scaffolding (`QueryHydrator`, `Source`, `Filter`, `Scorer`, `Selector`, `SideEffect`). The *names* are already in `algorithm-insights.md`; the file is too low-level. |
| `home-mixer/candidate_pipeline/for_you_candidate_pipeline.rs` | exclude  | Heavy with `xai_*` internal crate imports, mock vs prod clients, and Kafka side-effects. Needs internal X infrastructure to make sense; conceptual takeaway is already in the top-level `README.md`. |
| `home-mixer/candidate_pipeline/phoenix_candidate_pipeline.rs` | exclude  | 772 lines, same problem at larger scale. Adds confusion for Claude Code without adding ground truth the rubric can use.                                                                              |
| `phoenix/recsys_model.py`                                     | exclude  | 680 lines of JAX/Haiku model definition. The conceptual content (history attention, candidate isolation, `RecsysBatch`) is already in `phoenix/README.md` and the strategy docs.                     |
| `phoenix/recsys_retrieval_model.py`                           | exclude  | 388 lines of JAX retrieval-tower implementation. Same reason — concept is in `phoenix/README.md`.                                                                                                    |
| `phoenix/grok.py`                                             | exclude  | 616 lines of transformer architecture. Pure model internals; nothing here that the rubric or prompts need.                                                                                           |
| `phoenix/run_ranker.py`, `phoenix/run_retrieval.py`, `phoenix/runners.py`, `phoenix/test_*.py`, `phoenix/pyproject.toml`, `phoenix/uv.lock`, `phoenix/artifacts/` | exclude | All required only to actually run the original model. The new repo does not run the model.                                                                                                          |
| `home-mixer/` (entire tree, except the four small files above) | exclude  | Server, ads blending, hydrators, side-effects — all internal-X infrastructure. Bloat without ground-truth value for the rubric.                                                                      |
| `grox/`, `thunder/`                                           | exclude  | Content-understanding and in-memory store. Out of scope for a strategy-and-rubric app.                                                                                                                |
| `CODE_OF_CONDUCT.md`, `.gitattributes`                        | exclude  | Belongs to this repo, not the new one.                                                                                                                                                                |

### Why this list is short

The product is a strategy and rubric layer, not a model reimplementation. Every Rust file kept is under 100 lines and is cited by name in at least one strategy doc. Every Python file kept either contains the illustrative weighted-score formula (`run_pipeline.py`) or is a README that explains a concept the rubric depends on. Bigger files were tested against the eight decision rules and excluded because either the same concept is already in a smaller file or the file requires internal X infrastructure to make sense.

---

## 3. Recommended new repo folder structure

A Next.js app skeleton, plus the docs and references kept side-by-side so the app can import them at build time or read them from the filesystem.

```
x-reach-lab/
├── README.md                          # new — product overview (not the toolkit README)
├── LICENSE                            # new — the app's own license, separate from xAI's
├── package.json
├── next.config.js
├── tsconfig.json
├── app/                               # Next.js App Router routes
│   ├── layout.tsx
│   ├── page.tsx                       # dashboard
│   ├── analyzer/                      # post-analyzer feature
│   ├── log/                           # post-log feature
│   ├── positioning/                   # bio / pinned / banner helper
│   └── api/
│       └── chat/                      # future Claude / ChatGPT proxy
├── components/
├── lib/
│   ├── docs.ts                        # loads docs/*.md into memory
│   ├── rubric.ts                      # parses content-rubric.md into typed weights
│   └── prompts.ts                     # composes grounding prompts (see §4)
├── docs/                              # COPIED FROM THIS REPO
│   ├── README.md
│   ├── algorithm-insights.md
│   ├── content-rubric.md
│   ├── my-positioning.md
│   ├── post-analysis-template.md
│   ├── post-log-template.csv
│   ├── posts.md
│   ├── profile.md
│   └── replies.md
├── reference/
│   └── x-algorithm/                   # COPIED VERBATIM FROM THIS REPO
│       ├── ATTRIBUTION.md             # new — explains provenance and Apache 2.0
│       ├── LICENSE
│       ├── README.md
│       ├── phoenix-README.md
│       ├── phoenix-run_pipeline.py
│       ├── weighted_scorer.rs
│       ├── oon_scorer.rs
│       ├── vf_filter.rs
│       ├── age_filter.rs
│       └── muted_keyword_filter.rs
├── prompts/
│   ├── system-analyzer.md             # new — system prompt for the analyzer agent
│   ├── system-reply-coach.md          # new — system prompt for the reply coach
│   └── grounding.md                   # new — assembled grounding bundle
└── data/                              # git-ignored; user's local post log lives here
    └── .gitkeep
```

Key choices:

- `docs/` and `reference/` are siblings. Strategy docs cite reference files with paths like `../reference/x-algorithm/weighted_scorer.rs` so a single rewrite at copy time keeps the citations clickable.
- `data/` is local-first and git-ignored. No analytics, no upload.
- `prompts/` is the only new content category outside `app/`. It composes the docs into a single grounding bundle the API integration uses.

---

## 4. Notes for future ChatGPT / Claude API integration

The strategy docs already specify a workflow ("Paste these three files first, then your draft"). The new repo should make that workflow automatic.

### Grounding strategy

A request to the model should always carry, in this order:

1. `docs/algorithm-insights.md` (the ground rules).
2. `docs/my-positioning.md` (the positioning constraint).
3. `docs/content-rubric.md` (the rubric).
4. The user's draft post and any feature-specific context.

Build a single `lib/prompts.ts` helper that assembles these at runtime so they are always in sync with the source markdown — do **not** hard-code the rubric in TypeScript.

### Caching

The grounding bundle (the three docs above) is large, static per session, and reused across every analyzer call. Use Claude API prompt caching with a `cache_control` block at the end of the grounding bundle so repeat calls in a session pay for the bundle once. This also matters for cost: a `post-analysis-template.md` fill-in is ~2K tokens of structured output against a ~10K-token grounding bundle, so cached input is the difference between a feature that is cheap to use and one that is not.

### Refusal patterns to bake in

The system prompt should explicitly refuse to:

- Invent claims about "the algorithm" beyond what `algorithm-insights.md` states. (The doc itself separates "stated in the code", "inference", and "what the repo does not prove" — preserve those tiers.)
- Broaden the positioning. If a rewrite drifts off-niche per `my-positioning.md` topics-to-avoid, the model should refuse rather than soften.
- Pick the final verdict. The user picks. The model scores and rewrites.

### Model selection

For the analyzer (long context, careful scoring), default to a Claude Sonnet-tier model. For inline rewrite suggestions (short, latency-sensitive), Haiku is enough. Make this configurable in `lib/prompts.ts` — do not bury the choice inside an API route.

### Citations

When the model makes a claim about the algorithm, require it to cite a path under `reference/x-algorithm/`. The app can render those as links to the actual files. This is the main reason the source reference files in §2 are copied at all: without them, citations are paraphrase.

### Local-first integrity

No telemetry. No "improve the rubric over time by sending data home". The product promise is local-first; the only network traffic should be the chosen model API.

---

## 5. Risks of copying too much

Each of these is a reason the exclude list in §2 matters.

1. **Confusing Claude Code.** A new repo with `home-mixer/`, `phoenix/recsys_model.py`, and `candidate_pipeline.rs` in it tells Claude Code it is a Rust + JAX recsys project. It is not. It is a Next.js app. Off-topic files will pull tool calls (test runs, type checks) in the wrong direction.
2. **Broken builds.** `home-mixer/` and `phoenix/run_pipeline.py` import `xai_*` and `haiku` / `jax`. Copying them as-is into a Next.js repo without putting them in a clearly inert `reference/` directory will confuse linters, dependency scanners, and CI.
3. **License surface.** Apache 2.0 requires preserving the LICENSE and the NOTICE-style attribution. Copying many files multiplies the surface for getting that wrong; copying few files keeps the attribution job small and obvious.
4. **Drift between strategy and code.** The strategy docs were written against a snapshot of this repo. If the new repo bundles 4,000+ lines of source and then the source here changes, the new repo has stale code that the strategy doc still cites. The smaller the bundled source set, the lower the upkeep cost.
5. **False authority.** Pasting `phoenix/recsys_model.py` into the new repo invites the model to reason about model internals as if they were the production model. The repo itself says they are a mini frozen checkpoint. The strategy is more honest if the new repo never copies the model code.
6. **Repo size.** `phoenix/artifacts/` is delivered via Git LFS as a ~3 GB archive. Bringing it would balloon the new repo from a normal Next.js app to a multi-GB clone.

---

## 6. Files explicitly not to copy

A short, named list for clarity. Anything under these paths should not appear in the new repo:

- `candidate-pipeline/` — generic pipeline scaffolding; concepts already in the docs.
- `home-mixer/` — internal X infrastructure; keep only the four small reference files listed in §2.
- `phoenix/` — model implementation, runners, artifacts, lockfiles; keep only `phoenix/README.md` and `phoenix/run_pipeline.py`.
- `grox/` — content-understanding service; out of scope.
- `thunder/` — in-network store; out of scope.
- `CODE_OF_CONDUCT.md`, `.gitattributes` — belong to this repo only.

---

## 7. Required attribution file

A new file the copy step must author. Lives at `reference/x-algorithm/ATTRIBUTION.md` in the new repo.

It must:

1. State that the files in `reference/x-algorithm/` are unmodified copies from `xai-org/x-algorithm`, with a commit hash captured at copy time.
2. Reproduce the Apache 2.0 license terms by including the `LICENSE` file alongside.
3. State that the new repo does not run, reproduce, or claim to reproduce X's production ranking system.
4. State the snapshot date so future readers know how stale the citations may be.

Do not copy any source file from §2 without this attribution in place.

---

## 8. Final recommendation

Copy **nine strategy files** and **nine source reference files** (eight code files plus `LICENSE`), plus author **one new `ATTRIBUTION.md`**. That is the full transfer.

The exclude list is the more important half of this plan. Hold the line on it. Every additional source file copied is a file Claude Code may try to lint, type-check, or refactor — and none of them are part of the product.

If a future feature genuinely needs another file from this repo, add it deliberately, one file at a time, with a citation in a strategy doc explaining why. The default is "do not copy more".
