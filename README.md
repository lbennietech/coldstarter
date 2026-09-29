# Coldstarter

**From idea to a self-improving project, without writing the code yourself.**

Copyright (c) 2026 Luke Bennie <lukebennie@gmail.com>. Licensed under [CC BY-NC 4.0](LICENSE).

## Why it exists

Most people who build with Claude Code have no process. They open a session, ask for a feature, look at the result, ask for the next one, and carry on until something breaks. That works for a weekend script. On anything bigger, the same problems keep coming up:

- **The model checks its own work.** The session that wrote the code also decides whether the code is good. Nothing independent looks at correctness, security or usability, so problems surface when a user hits them, not before.
- **Nothing stops a regression.** If tests exist at all, they were written alongside the feature and nobody runs them before a push. Performance gets worse a little at a time and nobody notices until it's slow.
- **Work happens one item at a time.** Each small fix gets its own round of prompting, testing and checking. Twenty small fixes cost twenty rounds.
- **Every task runs on the same model and effort.** Fixing a typo in copy costs the same heavyweight reasoning as redesigning the data model. Token spend climbs and there's no sense of where it went.
- **There's no memory between sessions.** Decisions, known quirks, rejected ideas and the reasons behind them live in a chat that's gone the next day. The next session rediscovers them, or contradicts them.
- **Priorities are whatever comes to mind.** There's no backlog, no evidence for why one change matters more than another, and no record of what was done or how long it took.

Claude Code already has the features to fix all of this: subagents with their own tools and model settings, skills that package a workflow into one command, hooks that enforce rules deterministically, and project files that persist decisions. What's hard is knowing how to put them together. You have to know the features exist, then design a team of agents that don't step on each other, decide which work deserves an expensive model, write the gates, and set up a backlog that stays honest. Most of the failure modes don't show up until they've already cost you something. A few examples:

- In the reference build, an implementer added a feature and silently changed unrelated behaviour. Only an independent review caught it.
- In the reference build, a claimed speed-up from 64 ms to 37 ms didn't reproduce when it was measured again.
- In the reference build, one hand edit to the backlog silently dropped 12 items.
- A push gate with too short a timeout doesn't block anything. Claude Code treats a hook that times out as a non-blocking error, so the push goes through unchecked.

Coldstarter packages the working answers into one document. Reviewers are read-only and have to cite evidence. Only implementers edit, and they're tiered by how hard the work is. Each role runs on the cheapest model and effort that does it well. A backlog scores and batches the work, so reviews and tests run once per batch, not once per item. Hooks block a push that fails the tests or the benchmarks. Decisions, targets and lessons are written into the project so the next session starts where the last one stopped. You get the process without having to learn it the hard way.

## What it does

Coldstarter is a specification you give to [Claude Code](https://claude.com/claude-code). It takes an idea, a business problem, a question or a product concept and turns it into three things:

1. **A confirmed problem and solution.** Claude interviews you, analyses the problem, and proposes a stack, a scale profile and design pillars, stopping for your decisions.
2. **A working first version**, in a git repository with the CI/CD flow your scale needs.
3. **A development framework that keeps improving the project:**
   - tests and correctness invariants
   - benchmarks with a regression gate
   - read-only specialist reviewer agents, including a dedicated security reviewer
   - tiered implementer agents
   - an evidence-based backlog with a triage and batching engine
   - `/audit`, `/iterate` and `/autoiterate` loops
   - documentation for users, developers and operators, kept true by a docs writer
   - enforcement hooks
   - token-efficient model routing
   - all the supporting docs

It's designed to scale from solo hobby projects to enterprise work. A **Solo / Team / Enterprise** scale profile sets:

- the git flow and quality gates
- environments and secrets handling
- the reviewer roster and how much autonomy the agents get
- compliance and operations work

It includes adaptations for web apps, internal tools, integrations, data pipelines and analyses, AI applications, CLIs and libraries, mobile apps, games and automation.

## How to use it

1. Create an empty folder for your project (or open an existing codebase), and copy `COLDSTARTER.md` into it.
2. Open Claude Code in that folder and say:

   > Read COLDSTARTER.md and launch a project for: *your idea, problem or question*

3. Answer the intake questions. Most have sensible defaults. Then confirm the problem analysis, the solution and the targets at each 🚦 gate.
4. When the launch finishes, run `/iterate` to ship the first batch of improvements, or `/autoiterate` to let it keep working through the backlog on its own (as `/loop /autoiterate`, it also resumes by itself after session limits). Run `/audit` occasionally to refill the backlog.

If a session ends partway through, open Claude Code in the same folder and say "continue the project launch". Progress and decisions are kept in `docs/PROJECT_PROGRESS.md`.

## The project team: agents, models and effort

Coldstarter sets up a fixed team of Claude Code agents in `.claude/agents/`, split into two kinds:

- **Reviewers and auditors** are read-only — they never get Edit or Write tools, so they can only report findings, not change code. The core roster (every profile gets these) covers domain correctness, performance, UX, product/experience, efficiency, code quality, security, and a "three personas" user tester (newcomer, power user, breaker). Scale or domain adds more: compliance, infra/SRE, data, accessibility, evaluation reviewers. Every finding has to cite evidence — a metric, a screenshot path or a `file:line` — or triage throws it away.
- **Implementers** are the only agents that edit, and they never commit, push, merge or deploy themselves. There are three tiers: a **Light** implementer for effort-1 work outside the core logic, an **Opus** implementer for core-logic, performance or security items, and a **Deep** implementer (Opus at high effort) for the project's hardest work — concurrency, numerical cores, cryptography, data migrations, architecture changes, A/B experiments.

Every agent's model and effort are set deliberately, not left on defaults, because agent work is the main cost once a project has an active backlog:

| Setting | Used for |
|---|---|
| Sonnet, medium (the session default) | UX, product, code-quality, compliance, infra and data reviewers; the Light implementer; the triage engine |
| Opus, medium | Domain-correctness, performance, security and evaluation reviewers; the Opus implementer |
| Opus, high | The Deep implementer only |
| Sonnet, low | Efficiency auditor, user tester, accessibility reviewer — checklist-style work that doesn't need deep reasoning |
| Haiku | Only for mechanical, proven-safe work, adopted after a trial |

The rule behind the table: *use the strongest model only where it's clearly better, and send routine or checklist work to cheaper models or lower effort.* Settings change one level at a time, with the date and reason recorded in `CLAUDE.md`, and get retuned at the first retrospective once real usage shows which agents earn their cost. Sessions switch models at the start, not mid-session, because prompt caching is per model.

How much the project should optimise for usage is asked up front. Phase 1 sets a **usage profile** next to the scale profile: **Lean** for a usage-limited plan (cost first), **Balanced**, or **Throughput** (speed and depth first). It shapes the agents' review depth, how broad audits are, whether Deep work and A/B experiments need a go-ahead, and how strictly the code is kept small and easy for agents to read.

Usage is measured, not guessed: every `/audit` runs a small usage report that prices the project's Claude Code sessions and agent runs from their local transcripts, without spending model tokens, and turns what it finds into backlog items. In the reference build the first such review found that the main session's ever-growing context, agents re-caching after idle gaps, and high-level code reviews cost far more than the choice of model. The defaults that came out of it (a compaction window, a one-hour cache for implementers, named review levels, no duplicated test runs) are built in.

## The development cycle: batching for speed

Once the first version ships, work doesn't flow item-by-item — it flows through an evidence-based backlog and a batching engine, because reviewing and testing forty single-item changes costs far more than reviewing and testing eight batches of five:

1. **`/audit`** (rare, expensive) runs every relevant reviewer in parallel in the background against the current build, always including the security reviewer. Their findings — each with evidence — go to `triage`, which discards anything unsupported, merges duplicates, scores everything by impact ÷ effort, and assigns a tier and a time estimate.
2. **Triage groups the backlog into batches.** Every batch has exactly one category and one tier, so its reviews only need to run once for the whole batch instead of once per item. Categories are capped by size and by how much they can interact with each other: `ui` and `tooling` batches can hold up to 15 items because polish rarely conflicts; `core`, `perf`, `security`, `infra` cap at 5 because those changes do interact; `data` caps at 3; anything Deep-tier, an A/B experiment, or an irreversible change runs `solo`, one item at a time through its own full pipeline.
3. **`/iterate`** runs one batch: pick it, brief a single implementer with every item in it (never two implementers on one working tree — they'd overwrite each other), run the tests once at the end, run that category's reviews once, triage any blockers back to the same implementer, then commit, push (or open a PR, depending on scale profile) and report actual time against the estimate. New requests you send mid-run are consolidated into the queue first.
4. **`/autoiterate`** loops step 3 batch after batch without waiting for you, stopping only when the backlog is done, something needs your decision, or something is broken.

The payoff is measured, not assumed: in the reference build, 14 small `ui` and `tooling` fixes shipped in about 35 minutes as batches, against an estimated 175–245 minutes if they'd been done one at a time. Every batch's actual time gets recorded next to its estimate, so the estimates get more accurate the more the project runs `/iterate`.

## What's in this repository

| File | What it is |
|---|---|
| `COLDSTARTER.md` | The specification itself: 17 launch phases, scale profiles, project-type adaptations, agent templates (including the full security reviewer), triage and batching rules, hook templates, and lessons from the reference build. |
| `LICENSE` | CC BY-NC 4.0: the licence summary and full legal code. |
| `CLAUDE.md` | Instructions for Claude Code when maintaining the spec itself. |

## Where it came from

Luke Bennie designed Coldstarter while building Pocket Universe, a browser gravity sandbox, from a first idea to a self-improving development loop over two days in September 2026, with Claude Code as the implementing collaborator.

Along the way, the method gained:

- evidence-based audits and a triage engine
- tiered model routing to keep token usage down
- batched iterations (14 fixes shipped in about 35 minutes, against an estimated 3–4 hours one at a time)
- time-tracked reporting
- a dedicated security reviewer

It was then generalised so it can launch other kinds of project at other scales.

**How proven is it?** So far, the full method has run end to end on one project: that reference build, a solo browser game. The Team and Enterprise profiles and the other project-type adaptations follow the same method, but they haven't yet been tested on real projects. If you launch something with Coldstarter, what worked and what didn't is exactly the evidence that improves it. Lessons from real use go into the spec's Appendix H.

## Licence

Coldstarter is licensed under [Creative Commons Attribution-NonCommercial 4.0](LICENSE) (CC BY-NC 4.0). You may use, share and adapt it for non-commercial purposes, as long as you credit Luke Bennie, link to the licence and say what you changed.

**Products you build with Coldstarter are yours, including commercial ones.** The licence grants that as an extra permission. The non-commercial condition covers only the spec itself: you can't sell, sublicense or repackage `COLDSTARTER.md`, or adaptations of it, without permission. To ask, contact Luke Bennie at lukebennie@gmail.com.

## Status

Version 1.4.0 (2026-09-29). Previously named Launchframe. The version history is at the top of `COLDSTARTER.md`.
