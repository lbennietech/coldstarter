# Coldstarter

**From idea to a self-improving project, without writing the code yourself.**

Copyright (c) 2026 Luke Bennie <lukebennie@gmail.com>. All rights reserved.

Coldstarter is a specification you give to [Claude Code](https://claude.com/claude-code). It takes an idea, a business problem, a question or a product concept and turns it into three things:

1. **A confirmed problem and solution.** Claude interviews you, analyses the problem, and proposes a stack, a scale profile and design pillars, stopping for your decisions.
2. **A working first version**, in a git repository with the CI/CD flow your scale needs.
3. **A development framework that keeps improving the project:**
   - tests and correctness invariants
   - benchmarks with a regression gate
   - read-only specialist reviewer agents, including a dedicated security reviewer
   - tiered implementer agents
   - an evidence-based backlog with a triage and batching engine
   - `/audit` and `/iterate` loops
   - enforcement hooks
   - token-efficient model routing
   - all the supporting docs

It works for solo hobby projects and for enterprise work. A **Solo / Team / Enterprise** scale profile sets:

- the git flow and quality gates
- environments and secrets handling
- the reviewer roster and how much autonomy the agents get
- compliance and operations work

It covers every kind of project: web apps, internal tools, integrations, data pipelines and analyses, AI applications, CLIs and libraries, mobile apps, games and automation.

## How to use it

1. Create an empty folder for your project (or open an existing codebase), and copy `COLDSTARTER.md` into it.
2. Open Claude Code in that folder and say:

   > Read COLDSTARTER.md and launch a project for: *your idea, problem or question*

3. Answer the intake questions. Most have sensible defaults. Then confirm the problem analysis, the solution and the targets at each 🚦 gate.
4. When the launch finishes, run `/iterate` to ship the first batch of improvements. Run `/audit` occasionally to refill the backlog.

If a session ends partway through, open Claude Code in the same folder and say "continue the project launch". Progress and decisions are kept in `docs/PROJECT_PROGRESS.md`.

## The project team: agents, models and effort

Coldstarter sets up a fixed team of Claude Code agents in `.claude/agents/`, split into two kinds:

- **Reviewers and auditors** are read-only — they never get Edit or Write tools, so they can only report findings, not change code. The core roster (every profile gets these) covers domain correctness, performance, UX, product/experience, efficiency, code quality, security, and a "three personas" user tester (newcomer, power user, breaker). Scale or domain adds more: compliance, infra/SRE, data, accessibility, evaluation reviewers. Every finding has to cite evidence — a metric, a screenshot path or a `file:line` — or triage throws it away.
- **Implementers** are the only agents that edit, and they never commit, push, merge or deploy themselves. There are three tiers: a **Light** implementer for effort-1 work outside the core logic, an **Opus** implementer for core-logic, performance or security items, and a **Deep** implementer (Opus at high effort) for the project's hardest work — concurrency, numerical cores, cryptography, data migrations, architecture changes, A/B experiments.

Every agent's model and effort are set deliberately, not left on defaults, because reasoning tokens are the main cost driver once a project has an active backlog:

| Setting | Used for |
|---|---|
| Sonnet, medium (the session default) | UX, product, code-quality, compliance, infra and data reviewers; the Light implementer; the triage engine |
| Opus, medium | Domain-correctness, performance, security and evaluation reviewers; the Opus implementer |
| Opus, high | The Deep implementer only |
| Sonnet, low | Efficiency auditor, user tester, accessibility reviewer — checklist-style work that doesn't need deep reasoning |
| Haiku | Only for mechanical, proven-safe work, adopted after a trial |

The rule behind the table: *use the strongest model only where it's clearly better, and send routine or checklist work to cheaper models or lower effort.* Settings change one level at a time, with the date and reason recorded in `CLAUDE.md`, and get retuned at the first retrospective once real usage shows which agents earn their cost. Sessions switch models at the start, not mid-session, because prompt caching is per model.

## The development cycle: batching for speed

Once the first version ships, work doesn't flow item-by-item — it flows through an evidence-based backlog and a batching engine, because reviewing and testing forty single-item changes costs far more than reviewing and testing eight batches of five:

1. **`/audit`** (rare, expensive) runs every relevant reviewer in parallel in the background against the current build, always including the security reviewer. Their findings — each with evidence — go to `triage`, which discards anything unsupported, merges duplicates, scores everything by impact ÷ effort, and assigns a tier and a time estimate.
2. **Triage groups the backlog into batches.** Every batch has exactly one category and one tier, so its reviews only need to run once for the whole batch instead of once per item. Categories are capped by size and by how much they can interact with each other: `ui` and `tooling` batches can hold up to 15 items because polish rarely conflicts; `core`, `perf`, `security`, `infra` cap at 5 because those changes do interact; `data` caps at 3; anything Deep-tier, an A/B experiment, or an irreversible change runs `solo`, one item at a time through its own full pipeline.
3. **`/iterate`** runs one batch: pick it, brief a single implementer with every item in it (never two implementers on one working tree — they'd overwrite each other), run the tests once at the end, run that category's reviews once, triage any blockers back to the same implementer, then commit, push (or open a PR, depending on scale profile) and report actual time against the estimate.

The payoff is measured, not assumed: in the reference build, 14 small `ui` and `tooling` fixes shipped in about 35 minutes as batches, against an estimated 175–245 minutes if they'd been done one at a time. Every batch's actual time gets recorded next to its estimate, so the estimates get more accurate the more the project runs `/iterate`.

## What's in this repository

| File | What it is |
|---|---|
| `COLDSTARTER.md` | The specification itself: 17 launch phases, scale profiles, project-type adaptations, agent templates (including the full security reviewer), triage and batching rules, hook templates, and lessons from the reference build. |
| `LICENSE` | Terms of use (all rights reserved). |
| `CLAUDE.md` | Instructions for Claude Code when maintaining the spec itself. |

## Where it came from

Luke Bennie designed Coldstarter while building [Pocket Universe](https://github.com/lbennietech/pocket-universe), a browser gravity sandbox, from a first idea to a self-improving development loop over two days in September 2026, with Claude Code as the implementing collaborator.

Along the way, the method gained:

- evidence-based audits and a triage engine
- tiered model routing to keep token usage down
- batched iterations (14 fixes shipped in about 35 minutes, against an estimated 3–4 hours one at a time)
- time-tracked reporting
- a dedicated security reviewer

It was then generalised so it can launch any project at any scale.

## Status

Version 1.0.4 (2026-09-28). Previously named Launchframe. The version history is at the top of `COLDSTARTER.md`.
