# Coldstarter

**From idea to a self-improving project, without writing the code yourself.**

Coldstarter is a single document you hand to an AI coding agent. It tells the agent how to take your idea from a first conversation to a working product, and then how to keep improving it with a team of specialist agents, a prioritised backlog and automatic quality gates, at a cost that fits your plan.

Copyright (c) 2026 Luke Bennie <lukebennie@gmail.com>. Licensed under [CC BY-NC 4.0](LICENSE). Products you build with it are yours, including commercial ones.

---

## Contents

- [In one minute](#in-one-minute)
- [Quick start](#quick-start)
- [How to read this README](#how-to-read-this-readme)
- [New to agent stacks? The ideas you need](#new-to-agent-stacks-the-ideas-you-need)
- [Why it exists](#why-it-exists)
- [What a launch gives you](#what-a-launch-gives-you)
- [How a launch runs: the 17 phases](#how-a-launch-runs-the-17-phases)
- [Four dials that size everything](#four-dials-that-size-everything)
- [The agent team](#the-agent-team)
- [The development loop](#the-development-loop)
- [Guard rails: hooks and CI](#guard-rails-hooks-and-ci)
- [Keeping usage and cost down](#keeping-usage-and-cost-down)
- [Code, docs and design that hold up](#code-docs-and-design-that-hold-up)
- [Staying lean: process has to earn its place](#staying-lean-process-has-to-earn-its-place)
- [Project types](#project-types)
- [Any platform, any version control](#any-platform-any-version-control)
- [What a launched project looks like](#what-a-launched-project-looks-like)
- [The 15 ground rules](#the-15-ground-rules)
- [FAQ](#faq)
- [What's in this repository](#whats-in-this-repository)
- [Where it came from, and how proven it is](#where-it-came-from-and-how-proven-it-is)
- [Licence](#licence)
- [Status](#status)

---

## In one minute

You copy `COLDSTARTER.md` into a folder, open your AI coding agent there (the reference platform is [Claude Code](https://claude.com/claude-code)), and tell it what you want to build. From there, the agent:

1. **Interviews you** about the problem, who it's for, what success looks like, your budget and your constraints. If the honest answer is "you don't need software for this", it says so.
2. **Proposes two or three ways to build it**, compares them side by side (requirements, complexity, running cost, lock-in and more), and recommends one with its reasons.
3. **Decides how much process the project deserves.** A one-off script gets almost none. A product that will keep changing gets the full framework.
4. **Builds a working first version** and shows it to you.
5. **Sets up the machinery to keep improving it:** tests, read-only reviewer agents that must cite evidence, implementer agents tiered by difficulty, a backlog that scores and batches work, hooks that block bad changes, and docs that stay true.

After that, a single command (`/iterate`) ships the next batch of improvements, and another (`/autoiterate`) keeps going on its own until the backlog is done or it needs you.

It stops at every important decision (marked 🚦 in the spec) and waits for you.

---

## Quick start

1. Make an empty folder for your project (or open an existing codebase) and copy [`COLDSTARTER.md`](COLDSTARTER.md) into it.
2. Open your AI coding agent in that folder and say:

   > Read COLDSTARTER.md and launch a project for: *your idea, problem or question*

3. Answer the intake questions. By default they come in one round of about 15, each with a suggested default, so "defaults are fine" is a valid answer to most. For high-stakes or vague projects the agent suggests **grill mode** instead: one question at a time, each with a recommended answer, until every decision the plan depends on is settled. You can switch modes at any time.
4. Confirm or correct the agent's proposals at each 🚦 gate.
5. When the launch finishes:
   - **Standard or Full project:** run `/iterate` to ship one batch, `/autoiterate` to keep going unattended, and `/audit` now and then to refill the backlog. `/devmanual` reminds you how it all works.
   - **None or Light project:** the README's "Working on this" section explains how to change it.

**If a session ends partway through,** open the agent in the same folder and say *"continue the project launch"*. Every decision and the phase checklist live in `docs/PROJECT_PROGRESS.md`, so a fresh session picks up where the last one stopped.

**Example prompts:**

- *"Launch a project for: a one-page sales dashboard the marketing team can refresh themselves from the monthly CRM export."*
- *"Launch a project for: a browser-based physics sandbox where kids can throw planets at each other."*
- *"Launch a project for: should we move our overnight batch jobs to the cloud? I want an answer with numbers."*

---

## How to read this README

It's written for a range of readers. Jump to what you need:

| If you are... | Start with |
|---|---|
| **An IT professional new to AI coding agents** | [The ideas you need](#new-to-agent-stacks-the-ideas-you-need), then [Quick start](#quick-start) and [What a launch gives you](#what-a-launch-gives-you) |
| **A developer who's used an AI coding assistant but not built an agent setup** | [Why it exists](#why-it-exists), [The agent team](#the-agent-team) and [The development loop](#the-development-loop) |
| **Experienced with agent stacks and wondering what's different here** | [Keeping usage and cost down](#keeping-usage-and-cost-down), [Staying lean](#staying-lean-process-has-to-earn-its-place), [The 15 ground rules](#the-15-ground-rules), then the spec's Appendices D (triage), G (hooks) and H (lessons) |
| **Deciding whether to use it at work** | [Four dials that size everything](#four-dials-that-size-everything), [Guard rails](#guard-rails-hooks-and-ci), [How proven it is](#where-it-came-from-and-how-proven-it-is) and [Licence](#licence) |

---

## New to agent stacks? The ideas you need

An **AI coding agent** is an AI model that works inside your project folder: it reads files, runs commands, edits code and runs tests, rather than just answering questions in a chat. Coldstarter tells that agent how to organise itself. These are the building blocks it uses. The names are neutral; the spec's Appendix I maps each one to Claude Code (and to any other platform you add).

| Term | What it means in plain terms | Claude Code example |
|---|---|---|
| **Project instructions file** | A short file the agent reads at the start of every session: the project's rules, targets and conventions. Coldstarter keeps it small because it's re-read on every turn. | `CLAUDE.md` or `AGENTS.md` |
| **Subagent** | A separate agent with its own job, instructions, tools and model. Coldstarter uses them as reviewers and implementers. | `.claude/agents/<name>.md` |
| **Skill** | A saved procedure you run with one command, loaded only when needed. | `/audit`, `/iterate` |
| **Hook** | A plain script (not AI) that runs automatically at a fixed moment, such as after every file edit or before a push, and can block the action. Hooks are how rules get *enforced* rather than just *requested*. | `.claude/hooks/before_publish.py` |
| **Model tier** | How capable, and how expensive, the model is. Coldstarter uses three: **strong**, **standard** and **light**. | Opus, Sonnet, Haiku |
| **Effort** | How long the model thinks before answering. More effort means better reasoning on hard problems, and more usage. | low, medium, high |
| **Context** | Everything the agent currently has in view: instructions, files it has read, the conversation. It's re-read on every turn, so a bigger context costs more on every turn. | |
| **Prompt cache** | A discount for re-reading context that hasn't changed. It expires after a short idle time, and re-caching a big context is expensive. | 5 minutes by default, 1 hour optional |
| **Compaction** | Summarising a long session's context so it stops growing. | `autoCompactWindow` |
| **Change review** | An automated review of a diff at a chosen depth. | `/code-review low` |
| **Browser tool** | Lets an agent open and use a web app like a person would, take screenshots and read the console. | Playwright MCP server |
| **Backlog** | The prioritised list of known work. In Coldstarter every item needs evidence. | `BACKLOG.md` |
| **Batch** | A group of similar backlog items shipped together, so tests and reviews run once for the group instead of once per item. | `B1`, `B2`… |
| **Publish gate** | A hook that runs the tests (and benchmarks, where they exist) before a push and blocks it if they fail. | Hooks `git push` |

If you've only used AI in a chat window, the big shift is this: instead of one AI doing everything, you get a **small team with separate roles**, where the ones who judge the work can't change it, and plain scripts enforce the rules that matter.

---

## Why it exists

Most people who build with an AI coding agent have no process. They open a session, ask for a feature, look at the result, ask for the next one, and carry on until something breaks. That works for a weekend script. On anything bigger, the same problems keep coming up:

- **The model checks its own work.** The session that wrote the code also decides whether it's good. Nothing independent looks at correctness, security or usability, so problems surface when a user hits them.
- **Nothing stops a regression.** If tests exist at all, nobody runs them before a push. Performance gets worse a little at a time and nobody notices.
- **Work happens one item at a time.** Twenty small fixes cost twenty rounds of prompting, testing and checking.
- **Every task runs on the same model and effort.** A typo fix costs the same heavyweight reasoning as redesigning the data model.
- **There's no memory between sessions.** Decisions, known quirks and rejected ideas live in yesterday's chat. The next session rediscovers them, or contradicts them.
- **Priorities are whatever comes to mind.** No backlog, no evidence for why one change matters more than another, no record of what was done or how long it took.
- **Or the opposite: process swallows the product.** An agent told to "do it properly" spends the budget of a small tool writing architecture docs and threat models instead of building the tool.

Modern coding agents already have the features to fix all of this: subagents, skills, hooks and project files. The hard part is knowing how to put them together, and most of the failure modes don't show up until they've already cost you something. From the reference build:

- An implementer added a feature and silently changed unrelated behaviour. Only an independent review caught it.
- A claimed speed-up from 64 ms to 37 ms didn't reproduce when it was measured again.
- One hand edit to the backlog silently dropped 12 items.
- The main orchestrating session alone used 47% of all usage, because one session ran for a day and a half without compacting and reached about 740K tokens, all re-read on every turn.
- A publish gate with too short a timeout doesn't block anything: on some platforms a hook that times out is a non-blocking error, so the push goes through unchecked.

Coldstarter packages the working answers into one document, so you get the process without learning it the hard way.

---

## What a launch gives you

1. **A confirmed problem and solution.** A one-page problem analysis, a side-by-side comparison of two or three solution shapes, a recommendation with a "Why this" that answers every row of the comparison, design pillars, and the profiles that shape everything after (below).
2. **A working first version** under version control, with the CI/CD flow your scale needs, built to the agreed scope and no further.
3. **A development framework, sized to the project,** that can include:
   - readable, conventional code a person can debug, commented to the level you choose
   - a short project instructions file that works as an index to everything else
   - for anything with a user interface, a visual direction and a design guide that steer away from generic, machine-made defaults
   - tests and correctness invariants; benchmarks with a regression gate only where a performance target warrants them
   - read-only reviewer agents (including a dedicated security reviewer) and tiered implementer agents
   - an evidence-based backlog with a triage and batching engine
   - the `/audit`, `/iterate`, `/autoiterate` and `/devmanual` commands
   - enforcement hooks, and CI that runs the same checks
   - token-efficient model routing and a usage report that measures where usage goes
   - documentation for users, developers and operators, kept true by a docs writer

---

## How a launch runs: the 17 phases

🚦 marks a gate where the agent stops for your decision. Between gates it makes the reasonable call, says what it chose, and keeps moving. It commits at the end of every phase. Smaller projects run only the phases in their dev plan.

| # | Phase | What happens |
|---|---|---|
| 1 | Intake and problem analysis 🚦 | Survey any existing code, classify the project, interview you (quick round or grill mode), write a one-page problem analysis, and recommend the scale profile, usage profile, comment level and project type |
| 2 | Solution design 🚦 | Compare 2–3 solution shapes, recommend one with its reasons and a runner-up, set non-functional requirements, design pillars, visual direction and v1 scope, then **size the framework** and write the dev plan |
| 3 | Repository and delivery 🚦 | Set up version control, the ignore list, licence and header conventions; ask before creating any remote or cloud resource; add CI and environments to match the scale |
| 4 | First working version 🚦 | Build v1, deterministic, steppable, inspectable and instrumented from day one; design tokens and design guide before the first screen; demo it to you |
| 5 | Architecture and testability | Architecture doc, domain-logic reference, operations notes, threat model, and the readable-code conventions |
| 6 | Project instructions file 🚦 | The project's "constitution", kept as a short index; you confirm the numeric targets, invariants and security controls |
| 7 | Tests and invariants | One test command with quick, full and screenshot modes; end-to-end, invariant, accessibility and security checks |
| 8 | Benchmarks | Only if a performance target could change a decision: none, a timed check, or full benchmarks with a regression gate |
| 9 | Tool access for agents | An environment helper, a browser tool with a fallback, never production access |
| 10 | Agent roster | The reviewers, implementers and triage agent the project needs |
| 11 | Backlog and batching | `BACKLOG.md`, priority classes, batch categories and caps, estimates, a mechanical consistency check |
| 12 | Skills | `/audit`, `/iterate`, `/autoiterate`, `/devmanual` |
| 13 | Model routing 🚦 | Choose a model tier and effort for every role, with reasons; build the usage report |
| 14 | Hooks and CI | Deterministic enforcement: formatter and quick check after edits, a secrets guard, a publish gate |
| 15 | Operations | Team and Enterprise hosted services: observability, SLOs, runbooks, cost alerts, tested backups |
| 16 | First audit, first batch, retrospective | Ask before the first audit, ship the first batch, then review what worked and prune what didn't |
| 17 | Handoff | User guide, a developers' guide sized to the project, all docs consistent, and a summary of the life cycle |

---

## Four dials that size everything

Coldstarter doesn't treat a marketing dashboard and a regulated enterprise service the same way. Four separate settings, chosen with you during Phases 1 and 2, decide how much process a project gets and how it runs.

### 1. Scale profile: how heavy the process is

| | **Solo** | **Team** | **Enterprise** |
|---|---|---|---|
| For | Personal, hobby, prototype | Startup, small business, small team | Organisation, regulated, many stakeholders |
| How work ships | Commit and publish to the main line; a local hook gates it | A PR per batch, merged after CI passes | PRs with required reviews; a human approves; the agent never self-merges |
| Quality gates | Local hooks | Local hooks plus the same checks in CI | CI is the authority, plus security, dependency and licence scanning |
| Secrets | An ignored `.env` file | The hosting platform's secret store | Vault or KMS with rotation; never in agent context |
| Agent autonomy | High | Medium | Low to medium, tools restricted, no production access |

Mixed cases are normal (a solo developer handling payments uses Solo's flow with Enterprise-grade security) and are recorded as decisions.

### 2. Usage profile: how much AI usage to spend

Chosen separately, because a hobbyist on an entry-level plan and a funded team on an API budget can run the same scale under very different limits.

| | **Lean** | **Balanced** (default) | **Throughput** |
|---|---|---|---|
| Suggested for | Usage-limited plans (Claude Pro) | Higher-tier or team plans (Max, Team) | Enterprise or API, where time matters more than tokens |
| Audits | Core or focused; full audit only on request | Full audit at major milestones | Full audit at each milestone |
| Deep work and A/B experiments | Ask before each | Ask before A/B experiments | Run as the backlog says |
| Change review depth | `low` for UI and tooling, `medium` otherwise | Plus `high` for the hardest items | `medium` by default, `high` for hard, security and data items |

Savings that cost no quality apply in every profile: compaction, turn caps, one-hour caches for agents that wait, tests run once and shared with reviewers, one triage call per batch.

### 3. Comment level: who will read the code

| Level | For | Comments |
|---|---|---|
| **Agents-first** | Code only agents maintain | File headers, and comments only where the reason isn't obvious |
| **Standard** (default) | A person reads it now and then | Plus doc comments on every public function, class and module |
| **Human-maintained** | Handoffs, teams, learning projects, long-lived code | Plus comments on every non-obvious block and a "how to debug this" note per module |

At every level, comments explain *why*, not *what*.

### 4. Framework size: which machinery exists at all

At the end of Phase 2, Coldstarter sizes the framework and writes a short **dev plan**: every document, agent, skill and gate the project gets, with a reason, and a **trigger** for each thing left out (for example "add benchmarks when a page takes over a second to load").

| Size | For | What it gets | Life cycle |
|---|---|---|---|
| **None** | One-offs nobody will change | The deliverable, a README, and a one-off security pass | Deliver. A later change is a new request |
| **Light** | Small or short-lived tools, dashboards, automations, internal utilities | A minimum set: README (doubling as user and developers' guide), readable code, a ~50-line instructions file, core and end-to-end tests with a secret scan and dependency audit, the security reviewer, the change review, three small hooks and `docs/TODO.md`. Everything else waits for its trigger | Ask for a change; it's implemented, tested, reviewed and shipped |
| **Standard** | Products and tools that will keep changing | The full framework, with only the agents the project type needs | `/audit` rarely, then `/iterate` or `/autoiterate` batch by batch |
| **Full** | Standard projects at Team or Enterprise scale, regulated, or hosted with uptime targets | Standard plus the scale's extra reviewers, with CI as the authority | As Standard, through PRs and human approval |

Sizes move both ways. A Light tool whose change requests keep piling up moves to Standard; a finished product that only gets occasional fixes can drop back to Light.

---

## The agent team

Coldstarter splits agents into people who **judge** the work and people who **do** it. That separation is the single biggest quality lever.

### Reviewers: read-only, and they must cite evidence

Reviewers never get edit tools. Every finding must cite a metric, a screenshot path or a `file:line`, or triage throws it away. Each reviewer also has a "Known quirks (not bugs)" list, so environment oddities don't come back every audit.

Reviewers are where usage multiplies (each one re-reads the code), so only three are **always on** in a Standard or Full project:

| Always on | Job |
|---|---|
| **Domain-correctness reviewer** | Core logic: correctness, edge cases, stability, invariants. Measures rather than guesses |
| **Code-quality reviewer** | The whole codebase: coupling, duplication, error handling, test gaps, readability |
| **Security reviewer** | Threat model, auth, injection, secrets, supply chain, headers and CSP, CI/CD and infrastructure, data protection, and LLM-specific risks. On every project sized Light or above, even the smallest hobby tool |

The rest are **conditional**: each is created only if the project needs the role, and runs in an audit only when its trigger fires.

| Conditional reviewer | Runs when |
|---|---|
| Perf profiler | A budget is missed, the benchmark comparison regresses, or a hot path changed |
| UX reviewer | UI code, styles, copy or design tokens changed |
| User tester (plays three personas: *newcomer*, *power user*, *breaker*) | Behaviour a user sees changed |
| Accessibility reviewer | UI or accessibility checks changed or fail |
| Docs writer (the one non-implementer allowed to edit, and only docs) | A mechanical drift check flags a doc |
| Infra/SRE, data, compliance, evaluation reviewers | Their areas changed |
| Efficiency auditor, product/experience designer | Only in a milestone audit (`/audit full`) |

### Implementers: the only agents that edit code

Three implementers share the same instructions, one per **work tier**. They implement, test and report, but **never commit, publish, merge or deploy** themselves.

| Implementer | Starting model | Takes |
|---|---|---|
| `implementer` | Standard, medium effort | **Routine** work: small items outside the core logic |
| `implementer-hard` | Strong, medium | **Hard** work: core logic, performance, security, the safety gates' own logic |
| `implementer-deep` | Strong, high | **Deep** work: concurrency, numerical cores, cryptography, data migrations, architecture changes, A/B experiments |

Every implementer report must list any behaviour change beyond the item's scope, before and after measurements for any performance claim, and improvements it noticed along the way as suggestions for triage, not as extra changes in the diff.

### Triage

A `triage` agent turns findings into the backlog, scores them, assigns tiers and time estimates, and groups everything into batches. It runs at medium effort: at low effort in the reference build, it broke its own batching rules.

---

## The development loop

Once v1 ships, a Standard or Full project doesn't work item by item. Work flows through an evidence-based backlog and a batching engine, because reviewing and testing forty single-item changes costs far more than eight batches of five.

### 1. `/audit` fills the backlog

| Command | Runs |
|---|---|
| `/audit` | The **core audit**: the three always-on reviewers, plus each conditional reviewer whose trigger fired |
| `/audit full` | The **milestone audit**: every reviewer, followed by a retrospective |
| `/audit <focus>` | Only the agents for that focus, such as `/audit security` or `/audit perf` |
| `/audit usage` | Only the usage report and triage, no reviewers |

Before any agent runs, the evidence is collected mechanically: tests and screenshots, benchmarks, scans, the usage report, **the list of files changed since the last audit** (which decides the conditional triggers), and a **docs drift check** (a doc whose code changed since the doc did, or that names something that no longer exists). Reviewers then run in parallel, in the background, and the report says which ran and why.

### 2. Triage prioritises: class first, then score

A plain impact ÷ effort ratio lets a cosmetic fix (2 ÷ 1 = 2.0) outrank a security hole (5 ÷ 3 = 1.67). So every item first gets a **priority class**, set by the kind of problem and the evidence it has, not by its score:

| Class | Holds | Evidence required |
|---|---|---|
| **P1** | Safety and security blockers | An exploit path, a scan result or a failing check |
| **P2** | Correctness and data-loss blockers | A failing test or invariant, or a reproduction |
| **P3** | User-visible regressions | The before and after, or the shipped item it matches |
| **P4** | High-impact improvements, and medium or low security findings | As any finding |
| **P5** | Efficiency and polish | As any finding |

Impact ÷ effort only orders items *within* a class, so inflating an impact score can't jump a class. Only you can pin an item higher.

**Good enough is a result.** An improvement (P4 or P5) becomes work only if it passes the **actionability test**. It's set aside when the behaviour is intentional, the benefit is negligible, the change adds more complexity than it earns, nobody would notice, the code is already within budget, or it serves no pillar, target or requirement. Set-aside findings are recorded as "won't do: good enough" so they don't keep coming back, and "nothing here is worth changing" is a valid audit result.

### 3. Batches group the work

Every batch has **one category and one work tier**, so its reviews run once for the whole batch.

| Category | Contents | Max size |
|---|---|---|
| `ui` | Presentation, layout, copy | 15 |
| `tooling` | Tests, benchmarks, tools, docs; the product doesn't change | 15 |
| `core` | Domain logic | 5 |
| `perf` | Speed and efficiency | 5 |
| `security` | Auth, input handling, secrets, CVE upgrades | 5 |
| `infra` | CI/CD, infrastructure, deployment | 5 |
| `data` | Migrations, pipelines, data fixes | 3 |
| `solo` | Deep work, A/B experiments, irreversible or conflicting changes | 1 |

Polish rarely conflicts, so it batches large. Core, security, infra and data changes interact, so they batch small. In the reference build, 14 small `ui` and `tooling` fixes shipped in about 35 minutes, against an estimated 175–245 minutes one at a time.

### 4. `/iterate` ships one batch

The steps: **intake** (fold in any requests you sent mid-run, merging duplicates) → **pick** a batch → brief **one** implementer with every item (never two on the same working tree; they'd overwrite each other) → run the **tests once** → run the category's **reviews once** → send blockers back to the **same** implementer to fix *only* the blockers → **finish**: record actual time against the estimate, commit, then publish (Solo) or open a PR (Team and Enterprise) → **report** as a table.

| Command | Does |
|---|---|
| `/iterate` | Run `B1`, the batch holding the top item |
| `/iterate B3` | Run a named batch |
| `/iterate ITEM-ID` | Run the batch containing that item |
| `/iterate ITEM-ID solo` | Run just that item |
| `/iterate B2 without ID` | Run a batch minus some items |
| `/iterate B3 on Hard` | Raise a batch's work tier |

### 5. `/autoiterate` keeps going without you

It loops `/iterate` batch after batch, with every quality gate still applied. It **stops** when the backlog is empty or blocked on you, only P5 polish remains (unless you asked for polish), a decision needs you, something is broken that one fix round couldn't repair, a limit you set is reached (`/autoiterate 3`, `/autoiterate until ID`), or you say stop.

- `/autoiterate stop` finishes the batch in flight and stops. `/autoiterate stop now` stops at the next safe point, never leaving half-finished edits.
- **Session and rate limits:** it checks the real clock (late notifications are normal), and under the platform's loop or scheduler it schedules its own wake-up for after the reset and carries on. In the reference build, an agent reported "resets 2pm" when it was already 4:45pm.

### 6. `/devmanual` explains the project on demand

A short developers' guide sized to the project: its size and life cycle, the commands that exist, the current state, and the upgrade trigger. `/devmanual full` prints the whole guide.

**Stop digging.** When the requested behaviour works, the tests pass, the review finds no blocker and the targets are met, the work ships. Anything noticed on the way becomes a finding for triage, not part of the change, so a simple fix doesn't turn into an architecture rewrite.

---

## Guard rails: hooks and CI

Some rules are too important to leave to a model's good behaviour. Hooks are plain scripts that enforce them deterministically.

| Hook | When | What it does |
|---|---|---|
| `after_edit.py` | After a file edit | Runs the formatter, linter and quick check on watched files; failures go back to the agent to fix |
| `before_publish.py` | Before a shell command | On a real publish of **this** repository (`git push`, or `svn commit`), runs the full tests (and the benchmark comparison, where there are full benchmarks) and blocks the publish if they fail. It works out which repository a command targets, lets pushes of other repositories through, and gates anything it can't determine |
| `protect_secrets.py` | Before an edit or command | Blocks writing likely secrets and reading `.env` or credential files into the agent's context |
| `protect_baseline.py` | Before an edit (full benchmarks only) | Blocks hand edits to the benchmark baseline |

Team and Enterprise run the **same commands in CI**, so local and CI checks can't drift. Gates are never bypassed, and gate hooks get a generous timeout (at least twice the time the tests and benchmarks take), because on some platforms a hook that times out doesn't block.

**Benchmarks, where they exist,** fight noise from day one: the median of at least three interleaved runs, the measuring method stamped into results, a 5% regression gate, and a set-aside A/B test to tell a real regression from a warm laptop. In the reference build, identical code swung 10–30% between back-to-back runs; the median of five interleaved runs agreed within ±3%.

---

## Keeping usage and cost down

Agent work becomes the main cost once a project has an active backlog. Coldstarter treats usage as something to measure and design for, not guess.

### Model routing chosen per project

Once the project type, architecture and dev cycle are known, the agent sorts the platform's models into strong, standard and light tiers, and gives each role a tier and an effort from how hard its work is *in this project*, what a miss would cost, how often it runs, and the usage profile. You confirm the table. The reference build's routing is the starting point:

| Tier and effort | Used for |
|---|---|
| Standard, medium (the session default) | UX, product, code-quality, compliance, infra and data reviewers; the Routine implementer; triage |
| Strong, medium | Domain-correctness, perf, security and evaluation reviewers; the Hard implementer |
| Strong, high | The Deep implementer only |
| Standard, low | Efficiency auditor, user tester, accessibility reviewer (checklist work) |
| Light | Only for proven-safe mechanical work, after a trial |

The rule: *use the strongest model only where it's clearly better.* An analysis project might move its data reviewer up; a static brochure site might need no strong-tier implementer at all. Settings change one level at a time, with the reason logged.

### Fix the shape before the model

The reference build's first usage review found that the model choice mattered much less than the **shape** of the work:

- the main session's ever-growing context (47% of all usage)
- one A/B experiment (about a fifth of all usage to that point), mostly re-caching implementers' context after idle gaps
- high-depth code reviews and duplicated test runs

So these are built in: the main session compacts at a set window (200K tokens by default), agents that wait get a one-hour cache, every agent has a turn cap, tests run once and the results go to every reviewer, reviewers are briefed with file paths rather than whole docs, and agents start fresh with a brief rather than as copies of a large session.

**Judge cost per finished task, not per token.** A weaker first pass that needs extra review rounds can cost more than a stronger model that gets it right the first time. That's why the logic of the safety gates always goes to the strong tier.

### The usage report

From Standard up, a plain script (`tools/usage_report.py`, no model calls) reads the platform's local session records and prices usage per session and agent type: runs, cost, turns, peak context, how much went to re-caching, and the size of every instruction file loaded at session start. Every `/audit` runs it and turns what it finds into backlog items, so the framework tunes its own cost.

---

## Code, docs and design that hold up

**Readable code, from the first line.** The language's standard style and idioms (with a formatter and linter where the stack allows), names that say what things are (with units: `timeoutMs`, `massKg`), a header on every file, files small enough to read whole, tests that read as specifications (`test_merge_conserves_momentum`), errors that name what failed and with which values, and quiet tools that print one line on success. Readability gives way only on a measured hot path, with the reason in a comment.

**Built for testing from day one.** All randomness goes through one seedable function, time and external inputs can be faked, the core loop can run without real time or real services, and tests can inspect internal state. That's what makes invariant tests, benchmarks and reproducible bug reports possible.

**Instructions as an index.** The project instructions file is re-read on every turn of every session, so it stays within about 150 lines *and* 10 KB (about 50 lines for Light): rules and numbers, plus a "Read when" list pointing to the docs. The reasons behind decisions live in the Decisions log. In the reference build the file was only 101 lines but 18 KB, most of it history no task needed.

**Documentation for everyone who touches the project.** A user guide (plain language, no code), an architecture doc, a domain-logic reference (the rules the system follows, reviewed by the domain-correctness reviewer), operations notes, a threat model, a developers' guide, and for Team and Enterprise, decision records, runbooks and more. Every behaviour change updates its doc in the same change, and the docs writer and drift check keep them true.

**Considered design.** Anything with a user interface starts from a visual direction (who it's for, three words for how it should feel, references, and what it must never look like) and design tokens, written before the first screen. The design guide lists the statistically likely defaults that make an interface look machine-made (purple gradients, the three-card landing page, cards around everything, buzzword copy) and allows each only when the direction calls for it. An organisation's own design system beats it.

---

## Staying lean: process has to earn its place

A framework built to find improvements will always find more, and a framework with lots of process will always want more process. Four ground rules push back:

- **The product comes first (rule 12).** Every document, agent, skill, hook and gate needs a reason that holds for *this* project. Intake asks what the project is worth, and for None and Light projects the framework work must come in well under the effort of building v1. You can cut anything directly, for example *"We're building a $100 internal utility. Don't write architecture documentation unless the complexity warrants it."* Each cut is logged with the trigger that would bring it back.
- **Good enough is a result (rule 13).** The actionability test, above.
- **Stop digging (rule 14).** When the work meets its bar, ship it.
- **The framework isn't sacred (rule 15).** At every retrospective, any process step, doc, reviewer, skill or hook that keeps producing nothing useful is removed or downgraded, however much the spec recommends it. The security minimum stays, and a safety gate isn't removed just for being quiet: a gate that never blocks may simply be doing its job.

---

## Project types

The method is the same for every kind of project; the roles and checks are renamed to fit. Appendix A of the spec maps each type to a suggested stack (lightest first), deployment, test tooling, what the domain-correctness reviewer checks, the invariants, and what to benchmark if a target warrants it.

| Type | Typical size | Example invariants |
|---|---|---|
| Consumer web app or site | Standard | No data loss; forms validate; auth enforced |
| Business SaaS or internal tool | Standard or Full | Ledgers balance; permissions enforced; audit log complete |
| Enterprise integration or middleware | Standard or Full | Nothing lost or duplicated; reconciliations match |
| Data or analytics pipeline | Standard | Row counts reconcile; reruns idempotent |
| Analysis or business question | None or Light | Same data gives the same numbers |
| AI or LLM application | Standard | Eval pass rate at target; no leaked secrets or personal data |
| Mobile app | Standard | No data loss across restarts or offline |
| CLI, library or SDK | Standard | Documented behaviour holds; no crash on bad input |
| Game or simulation | Standard | Same seed gives the same state; no NaN |
| Data view or dashboard page | Light | Totals reconcile; missing data shows as "no data", never 0 |
| Automation or spreadsheet | Light | Totals reconcile; reruns idempotent |

Data views get extra care: a metric dictionary, a refresh step the audience can run themselves, aggregation before embedding so a forwarded file holds no personal data, chart-quality checks, and delivery that works as an email attachment, offline and in print.

---

## Any platform, any version control

**Platform-neutral.** The phases and templates speak only in neutral terms (the agent, the project instructions file, subagents, skills, hooks, strong/standard/light tiers). Every platform-specific detail (file paths, settings keys, frontmatter, hook input and output, commands, the transcript format) lives in the spec's **Appendix I (Platform bindings)**, with Claude Code as the reference binding. To use another platform, map the terms and record the mapping in the project's Decisions log. `AGENTS.md`, the cross-tool convention many coding agents read, is preferred where supported.

**Version control is a choice.** Intake asks for Git (the default), another tool such as Subversion, dated snapshots of the folder, or none at all, and checks the tool is installed before relying on it (Git isn't on Windows by default). Git isn't the same as GitHub: it runs on your machine, and a hosted copy is optional. Standard and Full projects need a real tool, because the gates, rollback, parallel working copies and reviews depend on it. **Appendix K (Version control bindings)** maps committing, publishing, branches, reviews, setting a change aside and the rest to Git, Subversion, snapshots and none.

---

## What a launched project looks like

An example Standard project on Claude Code. Your project will have only what its dev plan includes, and the source layout follows your stack's conventions.

```
my-project/
├── CLAUDE.md                  # project instructions file: a short index
├── README.md
├── BACKLOG.md                 # batches, ready, in progress, rejected / won't do
├── BACKLOG_DONE.md            # shipped items, with actual time and commit
├── docs/
│   ├── PROJECT_PROGRESS.md    # dev plan, phase checklist, Decisions log
│   ├── ARCHITECTURE.md
│   ├── <DOMAIN>.md            # domain-logic reference (e.g. BUSINESS_RULES.md)
│   ├── OPERATIONS.md          # build, deploy, verify, roll back
│   ├── THREAT_MODEL.md
│   ├── DESIGN.md              # visual direction, tokens, defaults to avoid
│   ├── USER_GUIDE.md
│   └── DEV_CYCLE.md           # the developers' guide
├── .claude/
│   ├── settings.json          # model routing, compaction, hook wiring
│   ├── agents/                # reviewers, implementers, triage
│   ├── skills/                # audit, iterate, autoiterate, devmanual
│   └── hooks/                 # after_edit, before_publish, protect_secrets…
├── tools/
│   └── usage_report.py
├── bench/                     # only with full benchmarks
│   ├── baseline.json
│   └── results/
├── tests/
└── src/                       # or whatever your stack calls it
```

A Light project is much smaller: typically the code, tests, a README with "Working on this", a short instructions file, `docs/PROJECT_PROGRESS.md`, `docs/TODO.md`, the security reviewer and three hooks.

---

## The 15 ground rules

The spec opens with the rules the agent follows throughout. In brief:

1. **Interview before building.** No project code until you confirm the problem, scale and direction.
2. **Don't build what isn't needed.** If a spreadsheet, an existing product or a process change is the better answer, say so.
3. **Stop at the gates** (🚦); between them, make the reasonable call and say what you chose.
4. **Keep a progress file** with a Decisions log, so any session can resume.
5. **Commit at the end of every phase.** Never rewrite shared history; ask before anything remote, public, deployed or billed.
6. **Check the current docs** for the platform, CI and hosting before writing config.
7. **Adapt, don't transplant.** Rename roles for the domain, but keep their function.
8. **Security by default, at every scale.** No secrets in the repo, prompts or context; least-privilege tools; no production credentials; a security reviewer from Light up.
9. **Respect the token budget.** Set a usage profile and measure where usage goes.
10. **Write docs for a person who starts cold.**
11. **Write code a person can read and debug.**
12. **The product comes first.** Process has to earn its place.
13. **Good enough is a result.**
14. **Stop digging.**
15. **The framework isn't sacred.**

---

## FAQ

**Do I need to know how to code?**
No, though it helps. The agent writes the code, asks for your decisions in plain terms, and shows you the result. You'll get more out of it if you can read a test result or a diff, and the comment level lets you ask for code a person can maintain.

**Does it only work with Claude Code?**
No. Claude Code is the reference platform and the only one with a full binding so far. The method is platform-neutral, and Appendix I explains how to map it to another agent.

**Can I use it on an existing codebase?**
Yes. Open the agent in the existing folder instead of an empty one. Phase 1 surveys the stack, entry points, tests, CI and deployment first and tells you what it found.

**Won't all these agents burn through my usage?**
That's the problem the usage profile, the conditional reviewer roster, batching and the usage report exist to solve. On a usage-limited plan, choose Lean. The reference build was retuned for exactly that situation.

**Is it overkill for a small tool?**
It's designed not to be. Framework sizing gives a one-off almost nothing and a small tool a short minimum set, and the process budget checks that the framework work stays well under the effort of building the tool.

**What if a session dies partway through a launch?**
Open the agent in the same folder and say "continue the project launch". Progress lives in `docs/PROJECT_PROGRESS.md`.

**Can I skip parts of it?**
Yes. Tell the agent what to cut. Every cut is logged with the trigger that would bring it back. The only thing it'll push back on is the security minimum, and even that it will cut once it has stated the risk.

**Can I sell what I build with it?**
Yes. Products you build are yours. The licence only restricts selling or repackaging the spec itself.

---

## What's in this repository

| File | What it is |
|---|---|
| [`COLDSTARTER.md`](COLDSTARTER.md) | The specification itself: ground rules, scale and usage profiles, framework sizing, 17 launch phases, and appendices for project types (A), the instructions-file skeleton (B), agent templates (C), the full security reviewer (C2), triage rules (D), the backlog template (E), skill skeletons (F), hook templates (G), lessons from the reference build (H), platform bindings (I), the design-guide template (J) and version-control bindings (K) |
| [`LICENSE`](LICENSE) | CC BY-NC 4.0: the licence summary and full legal code |
| [`AGENTS.md`](AGENTS.md) | Instructions for any AI agent maintaining the spec, including the rules that it stays platform- and version-control-neutral |
| [`CLAUDE.md`](CLAUDE.md) | Imports `AGENTS.md`, for Claude Code |
| [`ROADMAP.md`](ROADMAP.md) | Ideas and planned changes for future versions |

---

## Where it came from, and how proven it is

Luke Bennie designed Coldstarter while building Pocket Universe, a browser gravity sandbox, from a first idea to a self-improving development loop over two days in September 2026, with Claude Code as the implementing collaborator. Along the way the method gained evidence-based audits and triage, tiered model routing, batched iterations, time-tracked reporting, a dedicated security reviewer, and a usage review that became the usage profiles and the instructions-as-an-index rule. It was then generalised to launch other kinds of project at other scales. Appendix H records 22 lessons from that build, each with what happened and what it cost.

**How proven is it?** So far, the full method has run end to end on one project: that reference build, a solo browser game. The Team and Enterprise profiles and the other project-type adaptations follow the same method but haven't yet been tested on real projects. The Subversion binding was written from standard usage and hasn't been run yet (it's on the roadmap).

If you launch something with Coldstarter, what worked and what didn't is exactly the evidence that improves it. Lessons from real use go into Appendix H, and ideas go into [`ROADMAP.md`](ROADMAP.md). Get in touch at lukebennie@gmail.com.

---

## Licence

Coldstarter is licensed under [Creative Commons Attribution-NonCommercial 4.0](LICENSE) (CC BY-NC 4.0). You may use, share and adapt it for non-commercial purposes, as long as you credit Luke Bennie, link to the licence and say what you changed.

**Products you build with Coldstarter are yours, including commercial ones.** The licence grants that as an extra permission. The non-commercial condition covers only the spec itself: you can't sell, sublicense or repackage `COLDSTARTER.md`, or adaptations of it, without permission. To ask, contact Luke Bennie at lukebennie@gmail.com.

---

## Status

Version 2.2.0 (2026-09-30). Previously named Launchframe. The full version history is at the top of [`COLDSTARTER.md`](COLDSTARTER.md).
