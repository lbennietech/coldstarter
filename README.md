# Launchframe

**From idea to a self-improving project, without writing the code yourself.**

Copyright (c) 2026 Luke Bennie <lukebennie@gmail.com>. All rights reserved.

Launchframe is a specification you give to [Claude Code](https://claude.com/claude-code). It takes an idea, a business problem, a question or a product concept and turns it into three things:

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

1. Create an empty folder for your project (or open an existing codebase), and copy `LAUNCHFRAME.md` into it.
2. Open Claude Code in that folder and say:

   > Read LAUNCHFRAME.md and launch a project for: *your idea, problem or question*

3. Answer the intake questions. Most have sensible defaults. Then confirm the problem analysis, the solution and the targets at each 🚦 gate.
4. When the launch finishes, run `/iterate` to ship the first batch of improvements. Run `/audit` occasionally to refill the backlog.

If a session ends partway through, open Claude Code in the same folder and say "continue the project launch". Progress and decisions are kept in `docs/PROJECT_PROGRESS.md`.

## What's in this repository

| File | What it is |
|---|---|
| `LAUNCHFRAME.md` | The specification itself: 17 launch phases, scale profiles, project-type adaptations, agent templates (including the full security reviewer), triage and batching rules, hook templates, and lessons from the reference build. |
| `LICENSE` | Terms of use (all rights reserved). |
| `CLAUDE.md` | Instructions for Claude Code when maintaining the spec itself. |

## Where it came from

Luke Bennie designed Launchframe while building [Pocket Universe](https://github.com/lbennietech/pocket-universe), a browser gravity sandbox, from a first idea to a self-improving development loop over two days in September 2026, with Claude Code as the implementing collaborator.

Along the way, the method gained:

- evidence-based audits and a triage engine
- tiered model routing to keep token usage down
- batched iterations (14 fixes shipped in about 35 minutes, against an estimated 3–4 hours one at a time)
- time-tracked reporting
- a dedicated security reviewer

It was then generalised so it can launch any project at any scale.

## Status

Version 1.0 (2026-09-28). The version history is at the top of `LAUNCHFRAME.md`.
