# Coldstarter

*From idea to a self-improving project: the project-launch uber-prompt for Claude Code.*

Copyright (c) 2026 Luke Bennie <lukebennie@gmail.com>. Licensed under CC BY-NC 4.0 (see `LICENSE`), with one extra permission: projects and products you build with Coldstarter are yours, including commercial ones. Selling, sublicensing or repackaging this spec, or adaptations of it, needs the author's written permission.

| | |
|---|---|
| **Author** | Luke Bennie ([lukebennie@gmail.com](mailto:lukebennie@gmail.com)) |
| **Version** | 1.5.0 (2026-09-29) |
| **Origin** | Designed by Luke Bennie while building Pocket Universe, a browser gravity sandbox, from idea to self-improving dev loop over 2026-09-27/28, with Claude Code (Anthropic's Claude Opus 5.5 and Sonnet 5) as the implementing collaborator. The development method it encodes came from Luke's direction: the audit and iterate loops, tiered model routing for token efficiency, batch streamlining, time-tracked reporting, the dedicated security reviewer, and generalising it for any project at any scale. |

### Version history

| Version | Date | Changes |
|---|---|---|
| 1.5.0 | 2026-09-29 | **Readable code for people, not just agents.** A new ground rule (11): code must be easy for a person to read and debug. Phase 5's "agent-readable code" becomes **readable code**, which always applies: the language's standard style guide and idioms, a formatter and linter where the stack allows them (run by the after-edit hook), names that say what things are (with units), file headers that say what the file holds, tests that read as specifications, and errors that name what failed and with which values. Phase 1 asks who will read and debug the code and sets a **comment level** (Agents-first, Standard or Human-maintained), chosen separately from the usage profile because it follows the code's readers, not the budget. Comments explain why, not what. Readability gives way only on measured hot paths and in generated output, with the reason recorded. Phases 3, 4, 6 and 14, the code-quality reviewer, the implementer template, the `CLAUDE.md` skeleton and the definition of done carry it. |
| 1.4.0 | 2026-09-29 | **Usage profiles.** Phase 1 now asks how much the project should optimise for Claude usage, and records a **usage profile** (Lean, Balanced or Throughput) next to the scale profile. The savings that cost no quality stay in every profile; the profile sets the real trade-offs: review depth, audit breadth, asking before Deep or A/B work, reviewer effort, the user tester's scope, the compaction window, and how strictly code is kept agent-readable. Phase 5 gains **agent-readable code** (small files split by concern, searchable names, quiet tool output), because every agent turn re-reads what it has read. Lesson 21 added. |
| 1.3.0 | 2026-09-29 | **Usage review and token-efficiency defaults**, from the reference build's first measurement of where its Claude usage went. A **usage report** (`tools/usage_report.py`, specified in Phase 13) prices the project's Claude Code usage from the local session transcripts, per main session and agent type, and prints findings in the audit format without spending model tokens. Every `/audit` runs it, and `/audit usage` runs it alone. New defaults: the main session pins its effort and compacts at 200K tokens; implementers keep a one-hour prompt cache; every agent gets a `maxTurns` guard; `/iterate` runs the tests once and briefs reviewers with the results, always names the `/code-review` level, and calls `triage` once per batch; forks are avoided in large sessions; on a usage-limited plan `/autoiterate` asks before Deep or A/B batches; changes to the safety gates' logic go to the Opus tier; shipped items move to `BACKLOG_DONE.md`. Lessons 19 and 20 added. |
| 1.2.1 | 2026-09-29 | `/autoiterate` gets an explicit off switch as an argument rather than a second command: `/autoiterate stop` finishes the batch in flight and stops; `/autoiterate stop now` stops at the next safe point without leaving half-finished edits; under `/loop`, both cancel the scheduled wake-up. |
| 1.2.0 | 2026-09-29 | Documentation for every profile, not just developer docs: a **user guide** (`docs/USER_GUIDE.md`), a **domain-logic reference** (the rules the system follows, reviewed by the domain-correctness reviewer) and **operations notes** (`docs/OPERATIONS.md`: build, deploy, verify, roll back) join README, ARCHITECTURE, DEV_CYCLE and the threat model. A new core agent, the **docs writer** (Sonnet, medium; edits docs only), writes them and checks every doc against the code in each `/audit`. `CLAUDE.md` gains a Documentation rule: every behaviour change updates its doc in the same change. Lesson 18 added. |
| 1.1.0 | 2026-09-29 | New `/autoiterate` skill: it loops the `/iterate` cycle batch after batch without waiting for the user, stops only when the backlog is done, a decision needs the user, or something is broken, and pauses and resumes on its own around session limits (run as `/loop /autoiterate` for unattended runs). `/iterate` stays a single cycle and gains step 0, Intake: requests the user sends mid-run are consolidated with queued items, then triage regroups and refreshes priorities, keeping the user's pins. A limit rule for both: on a usage or rate-limit error, check the real clock before pausing; if the reset has passed, resume. Lessons 16 and 17 added. |
| 1.0.7 | 2026-09-28 | The reference project's single-file layout is no longer presented as the default or as a lesson to copy: it was a starting choice that hardened into a rule the owner never set, and it has been dropped. Lesson 1 records what that cost. Phase 2 now asks for the stack to be recorded as a decision with a revisit trigger. The web-app and game stack suggestions no longer lead with "single file". |
| 1.0.6 | 2026-09-28 | Licence clarified: an additional permission makes clear that products built with Coldstarter, including commercial ones, belong to whoever builds them. The non-commercial condition covers only selling, sublicensing or repackaging the spec. No changes to the method. |
| 1.0.5 | 2026-09-28 | Public release under CC BY-NC 4.0 (previously all rights reserved). Removed the link to the reference project's repository. No changes to the method. |
| 1.0.4 | 2026-09-28 | Corrections from a code review of the reference implementation: memory growth is also a median (the worst run measures warm-up, not steady growth). `--baseline` checks the method recorded in the result it's saving, and refuses partial runs. Mismatch messages say whether to re-run or re-baseline. A cheap single-run mode for agents. Medians fix noise within a session, not drift between sessions, so the robust gate compares against the committed code in the same session. Gate hooks need generous timeouts, because a timed-out hook doesn't block. |
| 1.0.3 | 2026-09-28 | Benchmarks: interleave repeated runs across scenarios, take the worst run for memory growth, stamp the measuring method into results and baselines, and refuse mismatched comparisons. Lesson 5 updated with the measured result (±3% against 10-30% swings). |
| 1.0.2 | 2026-09-28 | Fix: the push-gate hook only gates pushes of its own repository (it follows `cd`/`Set-Location` and `git -C`, and fails safe when unsure). The old template gated every push made from the session, including other repos. Lesson 15 added. |
| 1.0.1 | 2026-09-28 | Renamed from Launchframe to Coldstarter (file `COLDSTARTER.md`, repo `lbennietech/coldstarter`). No changes to the method. |
| 1.0 | 2026-09-28 | First release, as Launchframe: scale profiles (Solo/Team/Enterprise), project types, 17 launch phases, core agent roster with a dedicated security reviewer, triage and batching engine, hooks and CI, model routing, lessons from the reference build. |

> **What this is.** A general launch pad for any serious project, for business or pleasure, solo or enterprise. You give it an idea, a business problem, a question to answer, a product or tool to build, or an integration to set up. It turns that into a working first version and a self-improving development framework. The framework includes:
>
> - a repository with a CI/CD flow to match
> - readable, conventional code that a person can debug, commented to the level you choose
> - tests and quality gates
> - performance and correctness measurement
> - specialist reviewer agents
> - an evidence-based backlog with a triage and batching engine
> - `/audit`, `/iterate` and `/autoiterate` loops
> - deterministic hooks
> - token-efficient model routing
> - all the supporting docs
>
> **How to use it.** Open Claude Code in an empty folder (or an existing codebase) and say:
>
> *"Read COLDSTARTER.md and launch a project for: <your idea, problem or question>."*
>
> Copy `COLDSTARTER.md` into the new folder first, or give Claude its full path.
>
> Claude interviews you, proposes a solution, a technology stack and a scale profile, builds v1, then sets up the whole framework, stopping at key decision points. When it's done, you run `/iterate` for one development cycle, or `/autoiterate` to let it keep working through the backlog on its own.
>
> **Where it came from.** The method was distilled from building a real project end to end (Pocket Universe, a browser simulation) and its development loop, including the mistakes. Nothing here is game-specific. Every role and rule is stated generically, and Appendix A maps it to each kind of project.

---

## 0. Instructions to Claude Code

You are launching a project for the user: from problem to working first version to a self-improving development loop. This file is your work order. Follow it phase by phase, in this order:

1. Understand the problem.
2. Choose the scale and shape.
3. Build something real.
4. Measure it.
5. Add reviewers.
6. Turn their reviews into a prioritised backlog.
7. Automate the loop.
8. Tune it for cost and speed.

### Ground rules

1. **Interview before building.** Phase 1 is a conversation. Don't write project code until the user confirms the problem statement, the scale profile and the solution direction.
2. **Don't build what isn't needed.** If the best answer is an existing product, a spreadsheet, a no-code tool, a one-off analysis or a process change rather than a software project, say so in Phase 1, with your reasons. Only launch the full framework if the user still wants it.
3. **Stop at the gates** marked 🚦. Between gates, make the reasonable call, say what you chose, and keep moving.
4. **Keep a progress file.** `docs/PROJECT_PROGRESS.md` holds the phase checklist and a **Decisions** log (date, decision, who made it, why). Its first line records where the framework came from: *"Launched with Coldstarter v<version> by Luke Bennie."* The project itself belongs to whoever the user names as its owner. A fresh session must be able to resume when the user says "continue the project launch".
5. **Commit at the end of every phase** (`Phase N: …`), following the git flow of the chosen scale profile. Never force-push to shared branches. Ask before creating remote repositories, making anything public, deploying, or touching cloud accounts, billing or production.
6. **Check the current docs** before writing config: Claude Code (agents, skills, hooks, settings, MCP) at code.claude.com/docs, and the chosen platforms' CI and hosting. The templates here reflect formats as of 2026-09. Verify them rather than assume.
7. **Adapt, don't transplant.** Every role has a domain equivalent (Appendix A). Rename and reshape it, but keep its *function*.
8. **Security by default, at every scale.** Never put secrets in the repo, in prompts or in agent context. Agents get the minimum tools they need. No agent gets production credentials. Respect any organisation policy or managed settings you find. Every project gets a dedicated **security reviewer** agent (Phase 10, Appendix C2), from the smallest hobby project up. Only the depth of its checks scales with the profile.
9. **Respect the token budget.** Ask about it in Phase 1 and set a **usage profile** (Lean, Balanced or Throughput; section 1) from the answer. It shapes the agent specs, the workflow and the code layout, not just the models. Measure where the usage actually goes (the usage report) rather than guess.
10. **Write docs for a person who starts cold.** Plain, direct sentences, tables for reference material, no filler.
11. **Write code a person can read and debug.** From the first line of v1: the language's standard conventions and idioms, clear names, the stack's usual file layout, tests that read as specifications, and comments at the level chosen in Phase 1 ("Readable code" in Phase 5). Readability gives way only where a measured need forces it, and the code says why.

---

## 1. Scale profiles

Pick one in Phase 1. It decides how heavy every later phase is. Anything not marked for a profile is skipped for it.

| Dimension | **Solo** (personal, hobby, prototype) | **Team** (startup, small business, small team) | **Enterprise** (organisation, regulated, many stakeholders) |
|---|---|---|---|
| Git flow | Commit and push to `main`. The local hook gates the push. | Short-lived branches, a PR per batch, squash merge after CI passes. | Protected `main`, PRs with required reviews (CODEOWNERS), signed commits if policy requires, release tags or branches |
| How `/iterate` ends | Push to `main` and deploy | Open a PR. Merge after CI and a review. | Open a PR. A human approves and merges. Claude never self-merges. |
| Quality gates | Local hooks | Local hooks, plus the same commands in CI (CI is required) | CI is the authority. Adds security, dependency and licence scanning and required status checks. |
| Environments | Local and production (or just a deploy link) | Preview deployments per PR, then production | Dev, staging and production defined as infrastructure-as-code, with promotion and rollback |
| Secrets | A git-ignored `.env` | The hosting platform's secret store | Vault or KMS, with rotation and least-privilege access. Never in agent context. |
| Security review depth | A dedicated security reviewer on every audit and on sensitive changes: OWASP-style checklist, secrets and dependency hygiene | Plus a threat model kept current, security regression tests, and dependency and secret scanning in CI | Plus a formal threat model, SAST/DAST/SCA/IaC scanning as required checks, findings ready for a pen test, and a compliance mapping |
| Extra reviewers | None | A compliance reviewer if the domain is regulated | Compliance, infrastructure/SRE, accessibility, data governance, architecture |
| Docs | README, a user guide, ARCHITECTURE, a domain-logic reference, operations notes, DEV_CYCLE and a threat model | Plus decision records in `docs/adr/`, CONTRIBUTING and a changelog | Plus runbooks, SLOs, a data classification and onboarding docs |
| Operations | None, or console logging | Error tracking and basic metrics | Logging, metrics, tracing, SLOs, alerting, and incident and rollback runbooks |
| Compliance | None | Privacy basics (GDPR/CCPA if personal data) | Whatever applies: SOC 2, ISO 27001, GDPR, HIPAA, PCI DSS, accessibility law. Plus an audit trail. |
| Agent autonomy | High: implements, commits and pushes | Medium: implements and opens PRs; people merge | Low to medium: implements and opens PRs; people approve. No production access. Tools restricted. |
| Audit cadence | Rarely | Each milestone | Scheduled, with CI-driven checks in between |

Mixed cases are normal. For example, a solo developer building something that handles payments uses Solo git flow but Enterprise-grade secrets handling and security review depth. Record every deviation in the Decisions log.

### Usage profiles

The scale profile sets how heavy the process is. The **usage profile** sets how much of the user's Claude usage the project spends to get its work done, and it's chosen separately: a solo hobbyist on a Pro plan and a funded team on an API budget can run the same scale profile under very different limits. Pick one in Phase 1.

Some savings cost nothing in quality, so every profile gets them: the usage report in every audit, a compaction window for the main session, a one-hour cache for agents that wait, turn caps, tests run once with the results passed to reviewers, one triage call per batch, and named `/code-review` levels (Phase 13). The profile only moves the real trade-offs:

| Dimension | **Lean** (usage-limited plan; cost first) | **Balanced** (the default) | **Throughput** (usage isn't a concern; speed and depth first) |
|---|---|---|---|
| Suggested for | Pro, or any plan where the user hits limits | Max, Team, or an API budget with headroom | Enterprise or API use where time matters more than tokens |
| Agent models and effort | Phase 13 routing; observe-and-report roles (efficiency, code quality, user tester, accessibility) at low | Phase 13 routing | Phase 13 routing, with Opus also for the UX and product reviewers, and the Light implementer tier used only for copy and styling |
| `/code-review` level | `low` for `ui`, `tooling` and docs; `medium` otherwise; `high` only when the user asks | `low` for `ui` and `tooling`; `medium` otherwise; `high` for Deep items | `medium` by default; `high` for Deep, security and data items |
| Deep items and A/B experiments | Ask before each one; prefer one well-argued approach over an A/B | Ask before A/B experiments | Run them as the backlog says |
| `/audit` | Focused audits by default (`/audit <focus>`); a full audit only when the Ready list runs thin, and the user tester's personas folded into the UX reviewer | Full audits rarely, focused ones in between | Full audits at each milestone |
| User tester in `/iterate` | Plays only the batch's changes, at the sizes they affect | Plays the batch's changes at desktop and phone sizes | Plays the batch's changes, plus a short newcomer pass every batch |
| Main session | Compacts at 200K; `/clear` between hand-run batches | Compacts at 200K | Compacts at 400K, for more continuity |
| Batch caps | As Phase 11 (bigger batches mean fewer review cycles, but don't raise the caps: failures get harder to isolate) | As Phase 11 | As Phase 11 |
| Code layout (see "Readable code" in Phase 5) | A rule: files split by concern and kept small, tool output quiet by default, checked in code review | Strong guidance | Guidance |

The usage profile doesn't set how much the code is commented: that's the **comment level** (Phase 5), chosen by who will read and debug the code. A Lean project maintained by people can still be Human-maintained; it just pays for longer files knowingly.

Record the chosen profile in the Decisions log and in `CLAUDE.md` ("Model & effort"). Revisit it at each retrospective with the usage report's numbers: a Lean project whose reviews keep missing real bugs should move a dimension up, and a Balanced project whose user keeps hitting limits should move one down, one dimension at a time.

---

## Phase 1: Intake and problem analysis 🚦

**Goal:** an agreed problem statement, project type, scale profile, usage profile and comment level, before any solutioning.

1. Read the user's input. If the folder already has code, survey it first (stack, entry points, tests, CI, deployment) and say what you found.
2. **Classify the input:**
   - **product:** an app, game, site or service for users
   - **internal tool or automation**
   - **integration:** connecting systems
   - **data or analysis:** answering a question with data
   - **AI/LLM application**
   - **library or CLI**
   - **infrastructure or platform**

   Some inputs are more than one of these. A business *question* ("should we expand into X?") usually becomes an analysis project, whose deliverable is a report or dashboard rather than an app. The same framework applies (Appendix A).
3. Ask the follow-up questions in **one grouped round**: at most about 15, each with a suggested default, so the user can answer "defaults are fine" to most of them.
   - **Problem and value:** what problem, for whom, what they do today, and why now. For business projects, add the stakeholders, the decision-maker, the business case and the KPIs the project should move.
   - **Success:** what "working" looks like, with measurable targets (speed, accuracy, cost, conversion, time saved, uptime).
   - **Form and platforms:** web, mobile, desktop, CLI, API, pipeline, report or dashboard, AI assistant. Desktop, phone or both. Online, offline or both.
   - **Scale:** expected users, data volumes and load, now and in a year. Team size, and who reviews and approves work.
   - **Constraints:** budget (build and running cost), deadlines, hosting or cloud preferences, and existing systems to integrate with.
   - **Security and compliance:** personal, financial or health data; authentication and SSO needs; regulatory regimes; data residency; audit needs.
   - **Stack:** languages, frameworks and clouds the team knows or mandates, or "no preference".
   - **Data:** sources, ownership, sensitivity, volume, and whether it's fresh or historical.
   - **Distribution:** public or private repo, open source or proprietary, the organisation or GitHub account, and where it deploys.
   - **Identity:** project name, author or organisation for commits and copyright headers, and licence.
   - **Working style:** how autonomous Claude should be, and how often the user wants to review.
   - **Usage plan and ethos:** the Claude plan (Pro, Max, Team, Enterprise, or API), whether the user hits usage limits, and how much the project should optimise for usage in its agent specs, workflow and coding style: cost first (Lean), balanced, or speed and depth first (Throughput). Suggest the profile from the plan (Pro → Lean, Max or Team → Balanced), and say in a line what each would change.
   - **Code readers:** who will read and debug the code: agents only, agents plus a person who looks in now and then, or people who will maintain it (a handoff, a team, a learning project, long-lived code). This sets the **comment level** (Phase 5): suggest Standard, or Human-maintained when people will maintain it, and say in a line that higher levels mean longer files, which every agent reading them pays for.
   - **Reporting:** how the user wants progress reported (tables, summaries, which columns).
   - **v1 scope:** the smallest version that would be worth having.
4. Write back a **problem analysis** of about one page:
   - the problem restated
   - the users or stakeholders and their jobs-to-be-done
   - success criteria (measurable)
   - constraints
   - risks and unknowns
   - what's out of scope for v1
   - the **recommended scale profile**, **usage profile**, **comment level** and **project type**, with reasons

   If a non-software answer is better, say so here (ground rule 2).
5. 🚦 **Gate:** the user confirms or corrects the problem analysis, the scale profile, the usage profile, the comment level and the project type.

---

## Phase 2: Solution design and tech proposal 🚦

1. Propose **2 or 3 solution shapes**, each with its main trade-off, and recommend one. Cover the stack, hosting, data storage, and how it integrates with existing systems.
2. Choose by these principles, in order:
   - **Fewest moving parts that meet the requirements, including the non-functional ones** (security, availability, compliance, scale). The reference project started as one HTML file with no build and no dependencies, which made its first version quick to test and ship, and later dropped the single-file rule as the simulation grew. Record the chosen stack as a decision with its reason and the signal that would make you revisit it, not as a rule that agents enforce forever (Appendix H, lesson 1). At enterprise scale, "fewest moving parts" still applies within the organisation's mandated platforms.
   - **The organisation's existing standards,** if there are any (cloud, language, CI, identity provider). These beat personal preference.
   - **Running cost that fits the budget.** Give a rough monthly cost for anything hosted.
   - **Testability:** can it be driven headlessly and deterministically (Phase 5)?
   - **Replaceability:** avoid lock-in the user wouldn't choose knowingly.
   - **If it calls an LLM:** default to the latest, most capable Claude models, with prompt caching, and an evaluation harness from day one.
3. List the **non-functional requirements** with targets: performance, availability (Team/Enterprise), security controls, accessibility level (WCAG 2.2 AA is a sensible default for anything user-facing), privacy, and data retention.
4. Propose **3 to 5 design pillars**: short principles every later decision is judged against. For example:
   - reference project: *Toys over goals*, *One click to chaos*, *Readable at a glance*, *Works everywhere*
   - business app: *Correct before clever*, *Two clicks to any answer*, *Never lose data*
   - internal tool: *Faster than the spreadsheet it replaces*
5. Propose a **v1 feature list** that can be built in one or two sessions, plus an architecture sketch (components, data flow, hot paths, trust boundaries).
6. **Team and Enterprise:** write the key choices as decision records (`docs/adr/0001-<title>.md`: context, decision, alternatives, consequences).
7. 🚦 **Gate:** the user confirms the stack, the non-functional requirements, the pillars and the v1 scope.

---

## Phase 3: Repository, git flow and delivery pipeline 🚦 (before creating any remote or cloud resource)

1. Run `git init`. Set the author identity **in the repo config only**. Use the default branch `main`.
2. Add:
   - `.gitignore` for the stack, plus build outputs, test and bench outputs, caches, `.env` and secrets
   - `README.md`
   - a licence or a proprietary notice
   - `docs/PROJECT_PROGRESS.md`
   - Team and Enterprise: `CONTRIBUTING.md`, and a PR template
   - Enterprise: `CODEOWNERS`, `SECURITY.md`, and a changelog convention
3. Set the **header convention** for new source files (the copyright or licence line, then a line or two saying what the file holds) and the **code conventions** ("Readable code" in Phase 5): the language's standard style guide or the organisation's own, a formatter and linter where the stack allows them (for example Prettier and ESLint, Black or Ruff, gofmt, rustfmt), the test framework's usual layout, and the comment level from Phase 1. Record them in `CLAUDE.md`.
4. 🚦 **Ask before creating the remote**: the account or organisation, public or private (for example `gh repo create`). Then:
   - **Solo:** push `main` and set up deployment (static hosting, a platform deploy).
   - **Team:** add CI, running the same test and bench commands the local hooks run, plus preview deployments per PR, and protect `main` so merging requires CI to pass.
   - **Enterprise:**
     - CI with required status checks, plus security, dependency (SCA) and licence scanning
     - branch protection with required reviews
     - environments (dev, staging, production) as infrastructure-as-code, with promotion and rollback
     - secrets in the approved store
     - deployment credentials that agents never see
5. Commit: `Phase 3: repository and delivery setup`.

---

## Phase 4: First working version (MVP) 🚦

**Goal:** something real that runs end to end, not scaffolding.

1. Build the v1 features in the chosen stack. Keep the structure as simple as the stack allows, and write to the code conventions from the first line ("Readable code" in Phase 5): clear code costs no more to write than unclear code, and far less than cleaning it up later.
2. Design for the development loop from day one:
   - **Deterministic by construction:** all randomness that shapes behaviour goes through one seedable function (for example `rand()`). Time and external inputs can be injected or faked in tests.
   - **Steppable:** the core loop or workflow can run without real time or real services (fixed steps, fake clock, recorded fixtures).
   - **Inspectable:** tests can read the internal state through a test hook, test API or debug endpoint that only exists in test mode.
   - **Instrumented:** timings for the core logic versus rendering or I/O, with near-zero cost when switched off.
   - **Secure basics:** input validation at the system's edges, no secrets in code, authentication done properly if it's in scope.
3. Update `README.md`: what it is, how to run it, how to use it.
4. Run it and use it yourself. Take screenshots if it's visual. Fix the obvious problems.
5. 🚦 **Gate:** show the user (a demo, screenshots or a preview link). Does it match what they had in mind? Apply the quick wins from their feedback.
6. Commit, following the scale profile's flow.

---

## Phase 5: Architecture documentation and testability groundwork

1. Write `docs/ARCHITECTURE.md`:
   - a table of the files or components
   - the code layout
   - **how one unit of work flows** (a frame, a request, a job, a pipeline run), as a small diagram
   - the **hot paths**, and where cost grows with scale
   - trust boundaries and data flows (Team and Enterprise)
   - determinism notes
   - test, bench, build and deploy commands
2. Fill any gaps in testability left over from Phase 4.
3. Write the **domain-logic reference** (`docs/<DOMAIN>.md`, for example `SIMULATION.md`, `BUSINESS_RULES.md` or `PIPELINE.md`): the rules the system follows and why, with their constants, edge cases and invariants, citing functions rather than line numbers. The domain-correctness reviewer checks it. ARCHITECTURE says where code lives; this says what it does.
4. Write **operations notes** in `docs/OPERATIONS.md`: how to build, deploy, verify what's live (a version or build stamp), and roll back. For Solo, a few lines. Team and Enterprise extend it into runbooks (Phase 15).
5. Write a **threat model** in `docs/THREAT_MODEL.md`: assets, actors, trust boundaries, entry points, top threats, mitigations. For Solo, half a page is enough, since even a static site has third-party scripts, user input and a deploy pipeline. For Team and Enterprise, add a **data classification** for every data store. The security reviewer keeps it current.
6. Set up **readable code**, for the people who debug it and for agents. Claude writes clear, conventional code as fast as unclear code, and it pays back in every debugging session, human or agent. And every agent turn re-reads what the agent has read so far, so the size of what it must read to make a change is a running cost.
   - **Always** (every profile and comment level; checked in code review and by the code-quality reviewer):
     - **Conventions:** follow the language's standard style guide and idioms (for example PEP 8, Effective Go, the Rust API guidelines, a well-known JavaScript or TypeScript guide) or the organisation's own, with a formatter and linter enforcing them where the stack allows. Prefer the well-known way to do a thing over a clever one.
     - **Names** say what things are: full words in the language's naming convention, units where they matter (`timeoutMs`, `massKg`), booleans that read as questions (`isVisible`, `hasErrors`), no single letters outside short loops and standard maths, and one name per concept across the codebase. Keep them distinctive and searchable, and cite code by file and function name, so people and agents find things with one search instead of paging.
     - **Structure:** the stack's conventional project layout; code split by concern into files that can be read whole (a few hundred lines, not thousands); functions that do one thing; and `docs/ARCHITECTURE.md` saying what lives where, so a reader opens only the files a change touches.
     - **File headers:** every source file starts with the header convention (Phase 3), including a line or two on what the file holds.
     - **Tests read as specifications:** the framework's usual layout, mirroring the source; names that state the behaviour (`test_merge_conserves_momentum`); arrange, act, assert; one behaviour per test; no logic beyond setup; and failure messages that show the expected and actual values.
     - **Debuggable errors:** fail loudly, with a message naming what failed and the values involved. Never swallow an error silently.
     - **Quiet tools:** tests, builds and benchmarks print a one-line summary on success and the details only on failure (with a `--verbose` flag for more).
     - **Generated output** (bundles, minified or compiled files) is marked as generated, and points readers at the source, which stays readable.
   - **Comment level**, chosen in Phase 1 and recorded in `CLAUDE.md` (Conventions):

     | Level | For | Comments |
     |---|---|---|
     | **Agents-first** | Code only agents maintain, where usage matters most | File headers, and comments only where the reason isn't obvious from the code (a workaround, a constraint, where a tolerance comes from) |
     | **Standard** (the default) | Most projects: a person reads the code now and then, to debug or review it | Plus a doc comment on every public function, class and module, in the language's convention (JSDoc, docstrings, Javadoc, rustdoc, XML doc comments): purpose, parameters with units, return value, errors |
     | **Human-maintained** | Handoffs, teams, learning projects, long-lived code that people will own | Plus doc comments on internal functions, a comment on every non-obvious block (algorithms, maths, state machines, concurrency), and a short "how to debug this" note in each module's header: what to inspect or log, and the known failure modes |

     At every level, comments explain *why* and name the constraints; they don't restate *what* the next line does, because a comment that repeats the code goes stale and misleads. A change that makes a comment wrong updates it. Higher levels make files longer, and every agent that reads a file pays for its length on each turn, so choose the lowest level that serves the people who will read the code.
   - **Where readability gives way:** a measured hot path may trade clarity for speed, with a comment giving the reason and the measurement; shipped output may be minified or bundled while the source stays readable. Nothing else is an exception.
   - Under a Lean usage profile the layout rules (small files split by concern, quiet tools) are rules, checked in code review; otherwise they're strong guidance (section 1).
7. Commit.

---

## Phase 6: `CLAUDE.md`, the project's constitution 🚦 (for the targets)

`CLAUDE.md` is loaded into every Claude session, so everything durable goes here (skeleton in Appendix B):

- **The project in brief:** what it is, a table of the important files, the scale profile.
- **Before every change ships:** the numbered routine. Run the tests, then the benchmark comparison, then `/code-review`, then the user-tester agent, then the domain-correctness reviewer if the core logic changed, then the **security reviewer** if a sensitive area changed (authentication, input handling, secrets, dependencies, headers/CSP, CI/CD, infrastructure, data access, LLM prompts or tools). Then push, or open a PR, and republish or deploy.
- **Targets:**
  - **Performance budgets** with numbers (for example "p95 API latency ≤ 200 ms at 50 requests/s", "60 fps for the everyday scenario", "a nightly job finishes in ≤ 15 minutes", "bundle ≤ 200 KB gzipped")
  - a **stretch target**, marked as a backlog goal
  - **no steady memory growth**
  - **reliability**, for Team and Enterprise (availability or error-rate SLOs)
  - **cost** (a monthly cloud budget)
- **Correctness:** the domain's **invariants** with tolerances (Appendix A).
- **Security and compliance:** the controls and regimes that apply, and what counts as a sensitive change.
- **Design pillars.**
- **Workflow:** audit, iterate, batches, bench, A/B experiments, git flow for this profile, unattended runs (off unless the user opts in).
- **Model and effort:** the routing table from Phase 13, with a date and reason for each setting.
- **Conventions:** commit authorship, header, the code conventions (style guide, formatter and linter, naming, test layout) and the comment level, platform and input conventions, "keep README in step", "all randomness through `rand()`".

🚦 **Gate:** propose the numeric targets, the invariants and the security controls, and get them confirmed. Then commit.

---

## Phase 7: Tests and invariants

1. **One test command** (for example `python tests/run_tests.py`, `npm test` or `make test`). Every check prints `ok` or `FAIL` (with the measured values on failure), and the run ends with `N/N checks passed`. Modes:
   - (default): everything
   - `--quick`: a few seconds; does it start and run cleanly?
   - `--screens`: saves screenshots or rendered outputs for reviewers (UI projects)
2. **Functional checks** that drive the real thing:
   - UIs: Playwright end to end across browsers and emulated devices, with real mouse and touch input for important gestures
   - services: API contract tests against a running instance with test data
   - pipelines: fixture datasets with the expected outputs
   - every run: no console, server or unhandled errors
3. **Invariant tests** for the domain (Appendix A). They should cover:
   - **determinism:** same inputs and seed give an identical state, hashed over *all* the state, not a sample
   - **conservation or consistency** within documented tolerances
   - **no corrupt values** under stress
   - **edge cases** at the limits users can reach
   - **stability** across the whole range of supported parameters
4. **Accessibility checks** for anything user-facing (axe-core or similar), to the agreed WCAG level.
5. **Security checks:**
   - **All profiles:** a security regression test for each fixed vulnerability; tests that authentication and permissions are enforced (if there are any); validation tests at each input boundary; a secret scan over the repo (for example gitleaks); a dependency audit (`npm audit`, `pip-audit`, `cargo audit`).
   - **Team and Enterprise:** all of the above in CI as required checks, plus licence scanning, SAST (Semgrep, CodeQL), IaC scanning (Checkov, tfsec) and, for web services, a DAST pass (OWASP ZAP baseline) against staging.
6. The runners **fail gracefully**: a missing tool or browser prints a one-line fix, not a traceback.
7. Commit.

---

## Phase 8: Benchmarks and the regression gate

1. Seeded, reproducible **scenarios**, defined as data:
   - an everyday case (the main budget)
   - a large or stretch case
   - a stress case
   - a long run (for memory and resource growth)
   - a constrained profile (a CPU-throttled or slow device, a slow network, a small instance)
   - services: load tests (k6, Locust) at the target throughput
   - pipelines: runs at 1× and 10× the data volume
2. **Metrics:** mean, p95 and max per unit of work; core versus I/O or rendering time; memory growth; artefact size; cost per unit of work if measurable.
3. **Commands:**
   - `bench` writes `bench/results/latest.json`
   - `--compare` diffs against the committed `bench/baseline.json` and fails on a regression over **5%**
   - `--baseline` records a new baseline
4. **Budget report:** each budget prints as `ok`, `miss` (logged to the backlog, doesn't block) or `FAIL` (blocks).
5. **Fight noise from day one.** In the reference project, identical code swung 10–30% between runs as the laptop heated up over a long session, and the baseline was re-recorded four times in two days. So:
   - Take the **median of at least 3 runs** (5 is better for short scenarios) for every metric, memory growth included, and record the baseline the same way. Don't take the worst run for memory: the first run pays one-time warm-up costs (caches, JIT), so the worst run measures warm-up rather than steady growth. The one exception is a correctness failure such as NaN: a failure in any run counts.
   - **Interleave the runs:** run each scenario once per round, round-robin, rather than all of one scenario's runs back to back. A machine that slows down mid-run then affects every scenario alike.
   - **Stamp the method** (for example "median of 5 interleaved runs") into the results and the baseline. `--compare` should refuse a baseline measured a different way, and say which to do: re-run, if *this run* used a non-default method, or re-baseline, if the *baseline* is outdated. `--baseline` must check the method recorded in the result it's about to save, not just the command-line flags (a `--no-run --baseline` could otherwise save a quick run), and must refuse partial runs.
   - **Give readers a cheap mode.** Agents that only need one number (such as memory growth) should use a single-run option like `--repeats 1`, instead of paying for a full median.
   - **Medians fix noise within a session, not drift between sessions.** A baseline recorded while the machine was running fast will fail later on unchanged code. The robust gate benchmarks the committed code and the working copy in the same session, interleaved, and compares the two. Keep the stored baseline for budgets and history.
   - Ignore metrics near the noise floor.
   - Prefer a dedicated, stable runner in CI (Team and Enterprise).
   - When `--compare` fails, stash the change and re-measure the untouched code (a stash/pop A/B test) before blaming the change.
   - **Re-baseline only after a genuine improvement.** If the machine got slower, ask the user, and commit the re-baseline on its own with a clear reason.
6. Commit the first baseline, and set the budgets in `CLAUDE.md` from real numbers.

---

## Phase 9: Tool access for agents

1. A **local environment helper** (for example `tools/serve.py`, `make dev`, a Docker Compose file) that starts the app and its dependencies if they aren't running, and waits until they're ready.
2. For UIs, configure the **Playwright MCP** (or Chrome DevTools MCP) in `.mcp.json` so agents can use the product, take screenshots and read the console. For services, give agents the test endpoint and seeded test data, **never production**.
3. **Plan for tools failing.** Every agent that uses MCP falls back to the scripted test runner and its screenshots, and says so in its report.
4. If there's a hosted copy or demo build, add an idempotent build script (it skips the rebuild when the source hash hasn't changed) that strips anything used only in development.
5. **Enterprise:** list which MCP servers and tools are allowed, and restrict agent tools accordingly (`tools:` in each agent's frontmatter, plus permission rules in `.claude/settings.json`).
6. Commit.

---

## Phase 10: Agent roster

Create the agents in `.claude/agents/` (templates in Appendix C).

### Reviewers and auditors (read-only: never give them Edit or Write tools)

Each reviewer reads `CLAUDE.md` first. **Every finding needs evidence**: a metric, a screenshot path or a `file:line`. Triage discards findings without evidence.

**Core roster** (all profiles):

| Role | Job | Default model / effort |
|---|---|---|
| **domain-correctness reviewer** | Reviews changes to the core logic for correctness, edge cases, stability and the invariants. Measures rather than guesses. | Opus / medium |
| **perf profiler** | Runs the benchmarks, reads the hot paths, proposes measured improvements tied to a budget. | Opus / medium |
| **UX reviewer** (user-facing) | Screenshots and live use at several viewport sizes: discoverability, feedback, hierarchy, touch, keyboard, contrast, reduced motion, accessibility. | Sonnet / medium |
| **product / experience designer** | Is it valuable and pleasant? First-minute experience, "aha" moments, missing capabilities, sharing. Small shippable ideas that serve the pillars. For business tools, "time to answer" and workflow fit. | Sonnet / medium |
| **efficiency auditor** | Bytes, dependencies, network, dead code, memory growth, running cost, what ships in each artefact. | Sonnet / low |
| **code-quality reviewer** | The whole codebase, not a diff: coupling, duplication, error handling, gaps in test coverage, and readability (the code conventions, names, file headers, test structure, comments at the chosen level, stale comments). Respects the stack decision. | Sonnet / medium |
| **security reviewer** | A dedicated security specialist, on **every project and every audit**, and on any change that touches a sensitive area. Covers the threat model, authentication and authorisation, input handling and injection, secrets, dependencies and supply chain, headers and CSP, CI/CD and infrastructure config, data protection, and LLM-specific risks. Full spec in Appendix C2. | Opus / medium |
| **docs writer** | Writes the user guide, the domain-logic reference and the operations notes, and in every `/audit` checks each doc against the code and product, flagging drift and features that shipped undocumented. The one non-implementer allowed to edit, and only docs. Domain docs need their expert's review. | Sonnet / medium |
| **user tester** | Runs the tests with screenshots, then uses the product live as **three personas**: *newcomer* (the first 60 seconds, arriving cold), *power user* (builds something deliberate), *breaker* (spams input, extreme values, resizing, switching mid-action). Gives a ship verdict or audit findings. In `/iterate` it plays the batch's changes, starting from the test run it's given; the full three-persona sweep is for audits. | Sonnet / low |

**Added by scale or domain:**

| Role | When | Job | Default |
|---|---|---|---|
| **compliance reviewer** | Regulated domains | Checks the change against the applicable regime's controls, audit trail and data handling | Sonnet / medium |
| **infra / SRE reviewer** | Hosted services (Team and Enterprise) | Infrastructure-as-code, deployment safety, rollback, observability, SLOs, cost | Sonnet / medium |
| **data reviewer** | Pipelines, analytics, migrations | Schema, lineage, data quality, idempotency, reproducibility | Sonnet or Opus / medium |
| **accessibility reviewer** | Enterprise user-facing, or wherever the law requires it | WCAG conformance with evidence | Sonnet / low |
| **evaluation reviewer** | AI/LLM apps | Eval results, prompt regressions, grounding, refusals, cost | Opus / medium |

**Finding format** (every auditor uses it):

```
### [AREA-###] Short title
- **Area:** perf | core | ux | design | efficiency | code | security | infra | data | docs | usage | <domain>
- **Evidence:** <metric / screenshot path / file:line>
- **Impact:** 1–5   **Dev effort:** 1–5
- **Proposal:** what to change, why, and the expected gain
```

Give each agent a **"Known quirks (not bugs)"** list that grows over time. Quirks of the test environment shouldn't come back as findings every audit.

### Implementers (the only agents that edit)

There are three tiers with the same instructions. They implement, test and report per item. They **never commit, push, merge or deploy**.

| Agent | Model / effort | Takes |
|---|---|---|
| `implementer` | Sonnet / medium | **Light tier:** effort-1 items outside the core logic (ux, design, efficiency, code, docs) |
| `implementer-opus` | Opus / medium | **Opus tier:** core-logic, perf or security items, changes to the logic of the safety gates (hooks, the build, the test runner's pass/fail logic), or anything at effort 2+ |
| `implementer-deep` | Opus / high | **Deep tier:** the project's hardest class of work (for example concurrency, consistency or transactions, numerical cores, security-critical cryptography or authentication, data migrations, major architecture changes) and A/B experiments |

Every implementer report includes:

- what changed per item, with `file:line`
- the tests added, and the test result
- **any behaviour change beyond the item's scope.** In the reference project, the first iteration added per-scene defaults and silently made "restart" discard the user's own settings everywhere. The code review caught it.
- **before and after measurements** for any performance claim. A claimed 64 → 37 ms speed-up didn't reproduce, and the Done entry had to say "benefit unverified".

### Triage: the backlog and batching engine

`triage` turns findings into `BACKLOG.md` and groups the work into batches (Phase 11). Run it on Sonnet at **medium** effort. At low effort, its first attempt at grouping broke its own rules: it mixed tiers, put effort-3 items outside `solo`, and miscategorised a refactor of core-logic identifiers. The full rules are in Appendix D.

Commit the roster.

---

## Phase 11: Backlog, triage and batching

1. Create `BACKLOG.md` (template in Appendix E) with these sections: **Batches, Ready, In progress, Rejected**, and `BACKLOG_DONE.md` for **Done**. Shipped items live in their own file because everything that reads the backlog (the orchestrating session, triage) would otherwise pay for the whole history on every read.
   - **Ready** columns: ID, Area, Title, Impact, Effort, Priority, Batch, Tier, Est. time, Evidence.
   - **Done** columns: ID, Title, Tier, Actual time, Result (metric delta or notes), Commit or PR.
2. **Triage rules:**
   - discard findings without evidence, and merge duplicates
   - priority = impact ÷ effort
   - a finding that breaks a pillar goes to Rejected
   - never reorder In progress, Done or Rejected
   - a finding that matches a Done item (search `BACKLOG_DONE.md`) is a regression
   - IDs follow the pattern `AREA-###`
   - every Ready row gets a Tier and an Est. time
   - regroup every Ready item into batches on every run, and self-check the result
3. **Batching.** Each batch has **one category and one tier**. Its reviews run once, for the whole batch.

| Category | Contents | Reviews | Max size |
|---|---|---|---|
| `ui` | Presentation, layout, copy, UI affordances. No change to the core logic. | code-review + user tester | 15 (about 5 when they're effort-2 features) |
| `tooling` | Only tests, benchmarks, tools and docs. The product doesn't change. | code-review only. No user testing or redeploy. | 15 |
| `core` | The domain logic: business rules, calculations, simulation, workflows | code-review + user tester + one domain-correctness review | 5 |
| `perf` | Speed and efficiency work that isn't Deep tier | code-review + user tester + benchmark focus (+ correctness review if core is touched) | 5 |
| `security` | Authentication, authorisation, input handling, secrets, dependency upgrades with CVEs | code-review + security reviewer (+ user tester if the product's behaviour changes) | 5 |
| `infra` | CI/CD, infrastructure-as-code, deployment and configuration | code-review + infra/SRE reviewer, then a staging deploy (Enterprise) | 5 |
| `data` | Schema migrations, pipelines, data fixes | code-review + data reviewer, run against a copy first. Migrations are usually `solo`. | 3 |
| `solo` | Deep tier, A/B experiments, effort 3, irreversible changes, or anything that would conflict | That item's own full pipeline | 1 |

   The size caps limit the damage: bigger batches make it harder to tell which change broke something, spread reviewers' attention thinner, and make pulling out one bad item riskier. Core, security, infra and data changes interact. Polish rarely does. In the reference project, 14 small `ui` and `tooling` fixes shipped in about 35 minutes, against an estimated 175–245 minutes done one at a time.
4. **Estimates** (planning figures, not measurements):
   - Light, effort 1: 10–20 min
   - Opus, effort 1: 15–25 min
   - effort 2: 20–35 min
   - effort 3 or Deep: 35–90+ min
   - a batch: its largest item's estimate, plus 2–3 min per extra `ui` or `tooling` item and about 5 min per extra item in the other categories

   Record the **actual time** in Done, and recalibrate from real runs.
5. **Check bulk edits mechanically.** After any bulk change to the backlog, run a short script: every Ready item is in exactly one batch, tiers match, effort-3 items are `solo`, and no batch is over its cap. In the reference project, a single hand edit silently dropped 12 rows.
6. Commit.

---

## Phase 12: Skills: `/audit`, `/iterate` and `/autoiterate`

Create `.claude/skills/audit/SKILL.md`, `.claude/skills/iterate/SKILL.md` and `.claude/skills/autoiterate/SKILL.md` (skeletons in Appendix F). Don't set `effort` in skill frontmatter, so the session's `/effort` still applies.

**`/audit`**: rare and expensive; the backlog's source of truth.
1. Check the build is current and the tools work.
2. Collect evidence: the tests with `--screens`, `bench --compare`, the security scans (Team and Enterprise), and the usage report (`tools/usage_report.py --since last --save`, Phase 13), whose findings go straight to triage with no agent reading them.
3. Dispatch every relevant reviewer **in parallel, in the background**, always including the security reviewer and the docs writer (in audit mode). Brief each with the key numbers and the evidence paths. They're read-only and cite their evidence. Give them step 2's results so none re-runs the suite, and have reviewers that drive a UI start from the screenshots. When the UX reviewer and the user tester would both drive the same UI, fold the user tester's three personas into the UX reviewer's live pass: live UI use is the most token-hungry thing an agent does. If the project's agents aren't available as agent types, run general-purpose agents told to follow the matching agent file. A focused audit (`/audit perf`, `/audit security`) sends only the agents that cover the focus; `/audit usage` runs only the usage report and triage.
4. `triage` merges the findings and scores them, assigns Tier and Est. time, and regroups the batches.
5. Report: the headline numbers (including the usage report's top line), any usage finding that would change an agent's model or effort as a decision for the user (it trades quality for usage), the top 5 Ready items, the batches, and the decisions the user needs to make. Commit `BACKLOG.md` (in a PR, for Team and Enterprise).

**`/iterate`**: everyday, and batch-first.

| Command | Does |
|---|---|
| `/iterate` | Run `B1`, the batch that holds the top Ready item |
| `/iterate B3` | Run a named batch |
| `/iterate ITEM-ID` | Run the batch that contains that item |
| `/iterate ITEM-ID solo` | Run just that one item |
| `/iterate B2 without ID` | Run a batch minus some items |
| `/iterate B3 on Opus` | Raise a batch's tier |

0. **Intake.** Users send new requests mid-run. Before picking a batch (and whenever new requests arrive), write each one as a goal, with the user's own solution ideas recorded as context rather than requirements. Merge any request that overlaps a queued item into that item instead of adding a near-duplicate; move superseded items to Rejected. Then `triage` regroups the batches and refreshes priorities across the whole Ready list, keeping anything the user explicitly pinned. Tell the user in a line or two what was merged, added or re-ordered.
1. **Pick.** State the batch, its category, tier, items, reviews and Est. time. Move the items to In progress. Regroup first if the batches are stale. Team and Enterprise: create a branch named `batch/<id>-<slug>`.
2. **Implement.** Brief **one** implementer of the batch's tier with every item's row and proposal. It works through them as separate, isolated edits and runs the tests once at the end. **Never run two implementers on the same working tree**: they overwrite each other's uncommitted edits. Parallel work needs separate git worktrees. An item that turns out riskier than its category goes back to Ready.
3. **Test.** The full tests pass, run once with screenshots so the user tester reads them rather than running the suite again. Put the test and bench results in every reviewer's brief. If `bench --compare` fails, run the stash/pop A/B test to tell a real regression from machine noise.
4. **Review.** Run the category's reviews once, briefing each reviewer with the whole item list. Always name the `/code-review` level, since it otherwise reuses the last one typed: `low` for `ui` and `tooling`, `medium` for the other categories, `high` only for Deep items or when the user asks (it fans out to several sub-agents).
5. **Triage.**
   - Blockers go back to **the same implementer via SendMessage**, so it keeps its context. If that agent has already finished, start a fresh one and give it the full context.
   - Re-run steps 3–4 after the fix.
   - Drop an item from the batch rather than hold up the rest.
   - Everything else waits for the single `triage` call in step 7.
6. **Ratchet the baseline** only after a genuine improvement.
7. **Finish.**
   - Give each item its own Done row in `BACKLOG_DONE.md`. Record the batch's actual time once, on its first item.
   - Commit per item when the diffs separate cleanly. Otherwise, make one commit that lists every ID.
   - **Solo:** push (the hook gates the push), then deploy or republish if the product changed.
   - **Team and Enterprise:** open a PR with the batch table and test and bench results, let CI run, and hand it to the reviewers. Merge only if the profile allows it; Enterprise never self-merges.
   - Call `triage` **once**: it files the reviews' non-blocking findings and regroups the remaining items. Skip it if there's nothing to add and nothing was dropped.
   - **Report** to the user as a table (ID, Tier, Est. time, short description of the change), then actual against estimated time, the test and bench numbers, the PR link if any, and the next batch.

**When an agent hits a usage or rate limit** (in any skill): check the real clock first (`date`); task notifications can arrive late, and there's no other reliable clock. If the reset time has passed, check for half-finished edits and resume the same agent with SendMessage. If it's still in the future, tell the user the real reset time and how long that is from now, and carry on with work that doesn't need that agent. Retry a 429 that gives no reset time once before treating it as real. Quote the error as given; don't call a limit model-specific unless it says so.

**Keep the orchestrating session lean** (in any skill). It re-reads its whole context on every turn, which makes it the biggest single cost in a long run: read parts of large files (`Grep`, or a read with an offset and limit) rather than whole ones, open images only when you need to see them, point agents at files rather than pasting them, and ask for short reports. Use typed agents rather than forks, since a fork starts with a copy of the whole session. After a compaction, rebuild state from the backlog's In progress rows, `git status` and the list of running agents. When the user runs `/iterate` by hand, suggest `/clear` between batches: the backlog and git hold the state. If an agent stops at its `maxTurns` cap, resume it with SendMessage rather than starting over.

**`/autoiterate`**: the same cycle, looped. `/iterate` runs one batch and stops; `/autoiterate` repeats Intake → Pick → the full `/iterate` pipeline → a two-line report, batch after batch, without waiting for the user. Every quality gate still applies to every batch.
- **Turning it off:** `/autoiterate stop` finishes the batch in flight (through its commit and push or PR) and stops; `/autoiterate stop now` stops at the next safe point, committing what passes the gates or stashing the rest, never leaving half-finished edits. Under `/loop`, both also cancel the scheduled wake-up so it can't restart. No separate off command is needed.
- **Stop only when** the Ready list is empty or wholly blocked on the user; a decision only the user can make blocks the next useful work (ask once, and keep working on batches that don't depend on it); something is broken that one fix round couldn't repair; the user says stop; or an argument limit is reached (`/autoiterate 3` for three batches, `/autoiterate until ID`). On stopping, report every batch shipped in the run.
- **On a usage-limited plan,** treat a Deep `solo` batch or an A/B experiment as needing the user's go-ahead unless they've already given it for that item (in the reference build, one A/B experiment used about a fifth of all usage to that point). Ask once, and carry on with other batches meanwhile.
- **Never end a turn idle:** either an agent or command is in flight (its notification resumes the session), a wake-up is scheduled, or the loop has stopped for one of the reasons above.
- **Session limits:** apply the limit rule above. Run as `/loop /autoiterate` for unattended work: when the reset is in the future, it schedules its own wake-up for the reset time plus a couple of minutes (chaining wake-ups past the one-hour cap), re-checks the clock on waking and resumes. Plain `/autoiterate` loops just as well but needs a nudge after a limit.
- **Team and Enterprise:** each batch ends in its PR per the git flow, and the loop carries on with batches that don't depend on an unmerged PR (branching from the main branch). It stops when the next useful batch depends on a PR still waiting for human review.

Commit.

---

## Phase 13: Model routing and token efficiency 🚦 (confirm with the user)

Apply this policy unless the user says otherwise: *use the strongest model only where it's clearly better, and send routine or checklist work to cheaper models or lower effort.* The table below is the Balanced profile; apply the usage profile's changes from section 1 on top of it, and record the result in `CLAUDE.md`.

| Setting | Default |
|---|---|
| Session default | Sonnet at **medium**, pinned in `.claude/settings.json` (`"model": "sonnet"`, `"effortLevel": "medium"`), compacting at 200K tokens (`"autoCompactWindow": "200k"`) so a long run doesn't grow its context without limit |
| `/effort high` | In the main session, only for hard reasoning, then back to medium. Avoid xhigh and max. |
| Opus, medium | domain-correctness, perf, security and evaluation reviewers, and `implementer-opus`. Name `model: opus` explicitly (not `inherit`) so they stay on Opus in a Sonnet session. |
| Opus, high | `implementer-deep` only |
| Sonnet, medium | UX, product, code-quality, compliance, infra and data reviewers, `implementer`, `triage` |
| Sonnet, low | efficiency auditor, user tester, accessibility reviewer |
| Haiku | Only where it's proven good enough (for example mechanical formatting or lookups), after a trial |
| Agent guards | `maxTurns` on every agent, set well above a normal run, as a runaway guard. `experimental: cacheTtl: 1h` on the implementers and on any agent that waits more than five minutes between turns (benchmarks, reviews before a fix round): each expiry of the default five-minute cache re-caches the agent's whole context. |
| `/code-review` | Always with a level: `low` for `ui` and `tooling`, `medium` otherwise, `high` for Deep items or on request |

- **Judge cost per finished task, not per token.** In an agentic session most of the cost is re-reading cached context, which at 2026-09 prices costs the same per token on Sonnet and Opus (Opus is dearer only for new input and output; check current prices). A stronger model that finishes in fewer turns and fix rounds can cost less. The big levers are context size, cache expiry, duplicated work and agent fan-out, before the model.
- Change agent settings **one level at a time**, with a reason, and record the date and reason in `CLAUDE.md`.
- Switch models at the **start** of a session, because prompt caching is per model.
- Let batching save the cost: one cycle per batch, not per item. Run `/audit` rarely.
- Don't spawn agents for work that takes a couple of direct tool calls.
- Durable project preferences go in the checked-in docs. Personal preferences go in memory.

**Usage report.** Build `tools/usage_report.py` (a plain script, no model calls). It finds the project's Claude Code transcripts under `~/.claude/projects/<the project path, with every non-alphanumeric character replaced by '-'>/`: one `.jsonl` per session, and `<session>/subagents/*.jsonl`, each with a `.meta.json` naming the agent's type. It deduplicates each message's usage by message id, and prices input, cache writes (by their five-minute or one-hour lifetime), cache reads and output by model. It prints, per main session and agent type: runs, share of the total, cost and turns per run, peak context, and the share spent re-caching after idle gaps; then the most expensive runs; then findings in the audit format (`USAGE-###`, area `usage`) for a main session past the compaction window, agents re-caching after idle gaps, `/code-review` runs above medium, forks started from a large context, and agent types whose runs grew much longer or costlier since the last report. `--since last` covers the period since the last `--save`, which appends a summary to `.claude/usage-history.json`. It uses list prices as a proxy for plan usage, and says so in its output. It only sees sessions run on that machine: Team and Enterprise members run it on their own machines, or use the organisation's usage reporting where it has one. Take the first snapshot now, so the first retrospective has a baseline.

Commit `CLAUDE.md`, the agent frontmatter and the usage report.

---

## Phase 14: Hooks and CI: deterministic enforcement

**Local hooks** (all profiles): scripts in `.claude/hooks/`, wired in `.claude/settings.json` (templates in Appendix G).

| Hook | Event | What it does |
|---|---|---|
| `after_edit.py` | PostToolUse on Edit/Write/MultiEdit | If a watched file changed (source, test harness, bench scenarios), runs the formatter and linter on it (if the project has them), then the **quick** check. Exits 2 with the output on failure. |
| `before_push.py` | PreToolUse on Bash/PowerShell | On a real `git push` (matching `git [global options] push`, not `git stash push` or "push" inside a message) **of this repository**, runs the full tests and `bench --compare`. Exits 2 to block. It works out the push's target from any `cd`/`Set-Location` earlier in the command and from `git -C <dir>`, then asks git which repo that is. Pushes of other repos made from the same session pass through, and an undeterminable target is gated (fail safe). |
| `protect_baseline.py` | PreToolUse on Edit/Write/MultiEdit | Denies direct edits to `bench/baseline.json`. The baseline only changes through `--baseline`. |
| `protect_secrets.py` (all profiles) | PreToolUse on Edit/Write/Bash | Denies writing likely secrets (key patterns, high-entropy tokens), and reading `.env` or credential files into context. |

**CI** (Team and Enterprise) runs the same commands, so what's verified locally and what's verified in CI can't drift. Enterprise adds the security scans and required checks. Never bypass a gate (no `--no-verify`). When a gate blocks, find the root cause. Give gate hooks a generous timeout (at least 2× the time the tests and benchmarks take together): a PreToolUse hook that times out is a non-blocking error, so the push would go through unchecked.

Commit.

---

## Phase 15: Operations (Team and Enterprise, for hosted services)

1. **Observability:** structured logging, error tracking, key metrics (the performance budgets and the business KPIs) and, for Enterprise, tracing.
2. **SLOs and alerts** from the reliability targets in `CLAUDE.md`.
3. **Runbooks** (`docs/runbooks/`): deploy, roll back, rotate a secret, restore from backup, respond to an incident.
4. **Cost:** a budget alert on the cloud account, and a cost-per-unit metric where it's meaningful.
5. **Backups and restore**, tested once.
6. Add infra/SRE findings to the backlog as `infra` items. Commit.

---

## Phase 16: First audit, first batch, retrospective

1. Run the full "before every change ships" routine once on the current state, and fix the blockers.
2. 🚦 Ask before the first `/audit`, since it costs the most. Run it, then report the top 5 items and the batches.
3. Run the first `/iterate`. Watch for:
   - scope creep in the implementer's diff
   - noise in the benchmark gate
   - findings that repeat known quirks (add these to the agents' quirk lists)
4. **Retrospective with the user.** Look at actual against estimated time, token spend (from the usage report) against the usage profile, which agents found real problems and which produced noise, and whether the scale profile still fits. Tune the model and effort settings and the batch caps **one level at a time**, and record every change and its reason in `CLAUDE.md`.
5. Commit.

---

## Phase 17: Handoff

1. Write `docs/USER_GUIDE.md` (with the docs writer): everything a user needs, in plain language and without code — how to use every feature, what the outputs mean, limits and error messages. Link it from the product's own help where there is one. Then write `docs/DEV_CYCLE.md`, the developers' guide. It covers:
   - a table of the commands
   - the backlog columns (Tier, estimated and actual time)
   - the batch categories and why the caps exist
   - the steps of `/iterate`, `/autoiterate` and `/audit`, including intake and the limit rule
   - the git and PR flow for this profile
   - how to choose what to iterate on (by theme, cost, dependencies, fixes before features)
   - the A/B experiment convention
   - how to keep usage down
   - three worked examples using real batches from the backlog
2. Make sure `CLAUDE.md`, `DEV_CYCLE.md`, `ARCHITECTURE.md`, the user guide, the domain-logic reference, the operations notes, the skills, the agents, CI, the runbooks and `README.md` all agree. **Every workflow change updates all of them in the same commit or PR.**
3. Tick every phase in `docs/PROJECT_PROGRESS.md`. Once the development loop is live, mark it and this spec as archived history.
4. Give the user a brief summary of the dev loop, with its commands and the first batch to run.

### Definition of done

Items marked (T) apply to the Team profile, (E) to Enterprise, and (T/E) to both.

- [ ] Problem analysis, project type, scale profile, usage profile, comment level, stack, non-functional requirements and pillars confirmed (in the Decisions log)
- [ ] Repository with author identity, `.gitignore`, licence and header convention; remote, CI and deployment as agreed; branch protection and CODEOWNERS (T/E); environments as infrastructure-as-code (E)
- [ ] A working v1, shown to the user
- [ ] Code conventions recorded in `CLAUDE.md` (style guide, formatter and linter where the stack allows, naming, test layout, comment level), and the code follows them
- [ ] `docs/ARCHITECTURE.md` accurate; the system is deterministic, steppable and inspectable from tests; threat model (all profiles) and data classification (T/E)
- [ ] `CLAUDE.md` with files, the ship routine, numeric targets, invariants, security and compliance, pillars, workflow, model and effort, conventions
- [ ] Tests (full, quick and screens), the cross-platform matrix, invariant tests and accessibility checks all pass; security and dependency scans (T/E)
- [ ] Benchmarks with a median-of-N baseline, a 5% compare gate and a budget report
- [ ] Environment helper; MCP with a fallback; allowed-tool list (E)
- [ ] Read-only reviewers with evidence rules and quirk lists, **including the security reviewer**; the three implementer tiers; triage
- [ ] The first audit includes a security baseline; secret and dependency scans pass
- [ ] `BACKLOG.md` with the Batches, Tier, estimated and actual time columns, plus a mechanical consistency check
- [ ] `/audit`, `/iterate` and `/autoiterate` working end to end, batch-first, following the profile's git flow
- [ ] Hooks (formatter, linter and quick check, push gate, baseline guard, secrets guard); CI mirrors them (T/E)
- [ ] The usage report, run by `/audit`, with a first snapshot saved; the session's effort and compaction window pinned; agent turn caps and cache lifetimes set
- [ ] Observability, SLOs, runbooks and cost alerts (T/E, hosted services)
- [ ] First audit, first batch shipped, retrospective done, settings tuned
- [ ] `docs/USER_GUIDE.md`, the domain-logic reference and `docs/OPERATIONS.md` written and reviewed; `docs/DEV_CYCLE.md` written; all docs consistent; summary given to the user

---

## Appendix A: Adapting the framework by project type

| Project type | Suggested stack (lightest first) | Deploy | Test tooling | Domain-correctness reviewer checks | Invariants | Benchmarks |
|---|---|---|---|---|---|---|
| **Consumer web app / site** | Static site (plain HTML, CSS and JS); SvelteKit/Next.js + SQLite/Postgres | GitHub Pages, Vercel, Netlify | Playwright E2E (3 engines + devices), unit tests | business rules, state handling | no data loss; forms validate; auth enforced | Core Web Vitals, bundle size, p95 latency |
| **Business SaaS / internal tool** | The organisation's standard web stack + Postgres; SSO | Organisation cloud, Fly.io, Render | Unit, API contract, E2E, axe-core | business rules, authorisation, money maths, audit trail | ledgers balance; permissions enforced; idempotent writes; audit log complete | p95 latency at target load, queries per request, cost per user |
| **Enterprise integration / middleware** | The organisation's integration platform or a small service + a queue | Organisation cloud, infrastructure-as-code | Contract tests against system fakes, replayed messages | mappings, retries, ordering, failure handling | exactly-once or at-least-once holds; nothing lost or duplicated; reconciliations match | throughput, end-to-end latency, backlog drain time |
| **Microservices / platform** | Only if a monolith truly won't do; containers + infrastructure-as-code | Kubernetes, ECS, Cloud Run | Contract, integration and chaos tests | API compatibility, consistency, resilience | backward compatibility; consistency rules; graceful degradation | p95/p99 per service, error rate, cost |
| **Data / analytics pipeline** | Python + DuckDB/Polars; dbt for the warehouse | Scheduled jobs, Airflow, GitHub Actions | pytest with fixture datasets, data tests | joins, deduplication, schema drift, lineage | row counts reconcile; schema matches; reruns idempotent; reproducible | runtime and peak memory at 1× and 10× |
| **Analysis / business question** | Notebooks + DuckDB + a report or dashboard | Report, dashboard, slide deck | Reproducible notebook runs, data tests | methodology, statistics, bias, assumptions | the same data gives the same numbers; sanity checks hold | runtime, freshness |
| **AI / LLM application** | Claude API (latest model) + a thin backend | Vercel, Fly.io, organisation cloud | **An evaluation harness** as the invariant suite | prompt quality, grounding, tool use, refusals, safety | eval pass rate ≥ target; structured output validates; no leaked secrets or personal data | cost per task, p95 latency, cache hit rate |
| **Mobile app** | PWA first; then React Native/Flutter | Web, app stores | Device emulation, then device farms | offline behaviour, state restore, permissions | no data loss across restarts or offline | startup time, frame time, battery and network |
| **CLI / library / SDK** | Python, Go, Rust, TypeScript | PyPI, npm, crates, releases | Unit and golden-file tests on each OS | API contract, backward compatibility | documented behaviour holds; round-trips; no crash on bad input | throughput, startup, size |
| **Game / simulation** (the reference project) | Plain JS with Canvas/WebGL (one file or a few modules); or an engine | itch.io, Pages, stores | Playwright or engine test runner, invariant tests | numerical stability, conservation, determinism | same seed gives the same state; drift within tolerance; no NaN or tunnelling | fps per scenario, slow-device profile, memory |
| **Automation / spreadsheet** | Apps Script, Python + openpyxl, workflow tools | The user's workspace | Fixture-based tests | formula logic, dates, currencies | totals reconcile; reruns idempotent | runtime on large inputs |

Rename the categories and agents to fit the project. For example, the reference project's `sim` category is `core` here, and its physics reviewer is the domain-correctness reviewer.

---

## Appendix B: `CLAUDE.md` skeleton

```markdown
# <Project name>

<One paragraph: what it is, for whom, stack, where it runs. Scale profile: Solo | Team | Enterprise. `docs/ARCHITECTURE.md` explains the code; `docs/DEV_CYCLE.md` explains the dev loop.>

## Files
- `<main source>`: …
- `tests/…`: one command; `--quick`, `--screens`.
- `bench/…`: `bench/baseline.json` is committed; results are ignored.
- `tools/…`: environment helper, hosted-copy build.
- `.claude/agents/`, `.claude/skills/`, `.claude/hooks/`, `.claude/settings.json`, `.mcp.json`, CI config
- `BACKLOG.md`: batches and items, triaged.

## Before every change ships
1. Tests: every check passes.
2. Bench `--compare` when speed could change: no regression over 5%.
3. `/code-review`, and fix what it finds.
4. The user-tester agent checks the change.
5. Domain-correctness reviewer if the core logic changed. **Security reviewer** if the change touches a sensitive area (authentication, input handling, secrets, dependencies, headers/CSP, CI/CD, infrastructure, data access, LLM prompts or tools).
6. Solo: push (hook-gated) and deploy. Team/Enterprise: open a PR; CI and a person gate the merge.

## Targets & design pillars
### Performance and reliability budgets
- Everyday: … Stretch: … (backlog goal). Constrained profile: … Memory growth ≤ … Size ≤ …
- (Team/Enterprise) Availability/error SLO: … Cost ≤ …/month.
### Correctness
- Deterministic: … <domain invariants with tolerances>
### Security & compliance
- <controls, regimes, what counts as a sensitive change, where secrets live>
### Design pillars
1. … 2. … 3. …
### Workflow
- Audit (rare) · Iterate by batch (see DEV_CYCLE.md) · Bench run / --compare / --baseline (only on genuine improvement)
- Git flow: <profile's flow>. A/B experiments in two worktrees. Unattended runs: off unless opted in.
### Model & effort
- Usage profile: <Lean / Balanced / Throughput>, chosen <date> because <reason>.
- <Phase 13 routing table with the profile's changes, with dates and reasons; the session's effort and compaction window; the /code-review levels; the latest usage report's headline>
### Documentation
- Every change that alters behaviour updates the doc that describes it in the same change: USER_GUIDE (users), <DOMAIN>.md (the rules), ARCHITECTURE (code), OPERATIONS (deploy and rollback), THREAT_MODEL (entry points), README (the short version). The docs writer checks them all in /audit.
## Conventions
- Header: `Copyright (c) <year> <Owner>. All rights reserved.` (or the licence line), then a line or two on what the file holds.
- Code: <style guide>; <formatter and linter>, run by the after-edit hook. Names say what things are, with units. Tests: <layout>, names that state the behaviour, arrange/act/assert. Errors name what failed and the values.
- Comment level: <Agents-first / Standard / Human-maintained>, chosen <date> because <reason>. Comments say why, not what; update them with the code.
- Commits authored as <Name> <email> (repo git config). <Style, platform and input conventions.> Keep README in step. All randomness through `rand()`.
```

---

## Appendix C: Agent templates

**Reviewer (read-only):**

```markdown
---
name: <role>
description: <what it reviews and when to use it (in /audit, after X changes)>. Read-only.
model: sonnet            # opus for deep-reasoning roles; name it explicitly
effort: medium           # low for observe-and-report roles
maxTurns: 60             # runaway guard, well above a normal run
tools: Bash, Read, Glob, Grep   # + mcp__playwright for UI roles; never Edit/Write
---

You review <facet> of <Project>. Read `CLAUDE.md` (targets, pillars, security) and `docs/ARCHITECTURE.md` first.
You never edit, commit, push or deploy, and you never use production credentials. Throwaway experiments go in a temp folder outside the repo.

## Measure first
<commands that produce evidence: tests --screens, bench scenarios, scans, test hooks>
If your brief already has the test and benchmark results, use them rather than re-running the suite; spend your effort on targeted experiments.

## Look for
<domain checklist>

## Known quirks (not bugs)
- <grows over time>

## Report
Findings only, most valuable first, in the AREA-### format. No evidence, no finding.
(When reviewing a change rather than auditing: findings with file:line, a concrete trigger scenario and your confidence. Say plainly if nothing is worth fixing.)
```

**Docs writer** (edits docs only):

```markdown
---
name: docs-writer
description: Writes and audits <Project>'s documentation - user guide, domain-logic reference, operations notes, threat model - and checks in /audit that every doc still matches the code. Edits documentation only.
model: sonnet
effort: medium
tools: Bash, Read, Edit, Write, Glob, Grep   # + mcp__playwright to use a UI
---

You write and maintain <Project>'s docs. Read `CLAUDE.md` and `docs/ARCHITECTURE.md` first.
You edit only README and `docs/`; never code, tests, tools or agents; never commit, push or deploy.

## Writing
Confirm every claim in the code or the running product. Plain sentences for a reader who starts cold; tables for reference; one concern per doc, linking instead of duplicating. Domain docs need their expert's review before they ship.

## Auditing
Check every doc against the current code and product. Report each mismatch, and each feature that shipped without docs, as a DOC-### finding with evidence (doc line vs code or observation).
```

**Implementer** (three copies: `implementer` sonnet/medium, `implementer-opus` opus/medium, `implementer-deep` opus/high):

```markdown
---
name: implementer
description: Implements a <Project> BACKLOG.md batch (or a single item) as the smallest reasonable changes, one isolated edit per item. <Tier scope>. Use from /iterate.
model: sonnet
effort: medium
maxTurns: 100            # 150 for implementer-opus, 200 for implementer-deep
tools: Bash, Read, Edit, Write, Glob, Grep
experimental:
  cacheTtl: 1h           # it waits through test runs and reviews before a fix round
---

You implement backlog items for <Project>. Read `CLAUDE.md` first.
You implement and test. You never commit, push, merge or deploy.

## What to do
1. Make the smallest reasonable change that delivers each item, following the project's conventions and security rules. Write to the code conventions and comment level in `CLAUDE.md`: clear names, the language's idioms, comments that say why, and doc comments at the chosen level. Update any comment your change makes wrong.
2. Add or extend a test for new behaviour.
3. Run the full tests once at the end. If something fails, fix it or report exactly what's blocking. Don't work around it. Don't run the full benchmark comparison: /iterate runs it right after you (the exception is one arm of an A/B experiment, which benchmarks itself once, near the end).
4. Update README.md or the docs if behaviour or usage changed.

## Batches
Work through the items one at a time as separate, isolated edits. If an item turns out riskier than its category (for example a `ui` item that touches the core logic or security), skip it and flag it.

## Report (per item)
1. What changed, with file:line.
2. Tests added, and the test result.
3. Behaviour changes beyond the item's scope.
4. Before and after measurements for any performance claim.
5. Leftovers and follow-ups.
```

---

## Appendix C2: Security reviewer (full spec)

Every project gets this agent, whatever its scale. Solo projects run the core checklist; Team and Enterprise add the CI scanners and compliance mapping.

```markdown
---
name: security-reviewer
description: Dedicated security specialist for <Project>. Reviews the whole system in /audit, and any change that touches a sensitive area, against the threat model. Covers authentication and authorisation, input handling and injection, secrets, dependencies and supply chain, browser security headers, CI/CD and infrastructure, data protection, and LLM-specific risks. Read-only.
model: opus
effort: medium
tools: Bash, Read, Glob, Grep   # + mcp__playwright for web apps; never Edit/Write
---

You are the security reviewer for <Project>. Read `CLAUDE.md` (the security & compliance section) and `docs/THREAT_MODEL.md` first. Your job is to find real, exploitable weaknesses and risky patterns, backed by evidence, and to keep the threat model current.

## Rules of engagement
- Read-only. You never edit, commit, push or deploy.
- Test only the local or test environments you were pointed at. Never production, never third-party systems, never real user data.
- Prove each finding with the smallest safe demonstration: a failing test case, a crafted input against the local app, or `file:line` with a clear explanation of the data flow. Don't write weaponised exploits.
- Never print, copy or pass on secrets you come across. Report where they are (`file:line` and the type of secret), not their value.

## Measure first
- Run the project's security checks: the secret scan (for example gitleaks), the dependency audit (npm audit, pip-audit or cargo audit) and, where configured, SAST (Semgrep or CodeQL), IaC scanning and a DAST baseline.
- Map the attack surface:
  - every entry point: routes, forms, file uploads, URL and query parameters, message consumers, CLI arguments, webhooks, LLM tool calls
  - every trust boundary
  - every place data leaves the system

## Checklist (apply what's relevant to the project)
- **Authentication and sessions:** credential handling, MFA and SSO integration, session fixation, cookie flags (HttpOnly, Secure, SameSite), token lifetime, logout and revocation.
- **Authorisation:** every object access is checked server-side (IDOR), role and tenant isolation, privilege escalation, admin paths.
- **Input handling and injection:**
  - SQL and NoSQL, command, template and XSS injection (reflected, stored, and DOM, including `innerHTML`)
  - path traversal, SSRF, open redirects
  - unsafe deserialisation, XXE, regex denial of service
  - parsing of URL or share-link state
- **Browser security:** CSP, HSTS, frame-ancestors, CORS policy, third-party scripts and fonts (Subresource Integrity), clickjacking, postMessage handling.
- **Secrets:** none in the repo, git history, logs, client bundles, error messages or agent context. Keys can be rotated and have least privilege.
- **Dependencies and supply chain:**
  - known CVEs; abandoned or typosquatted packages
  - lockfiles committed
  - versions pinned, including CDN URLs, and GitHub Actions pinned to commit SHAs
  - build scripts that fetch code at runtime
- **CI/CD and infrastructure:** least-privilege workflow tokens, `pull_request_target` misuse, secrets exposed to forks, public buckets, open security groups, missing encryption, default credentials, IaC misconfiguration.
- **Data protection and privacy:** personal data minimised, encrypted in transit and at rest, retention and deletion, logs free of personal data and secrets, data residency, protected backups.
- **Abuse resistance:** rate limiting; resource limits (payload size, uploads, expensive queries, compute bombs); account enumeration.
- **Cryptography:** vetted libraries only, no homemade crypto, modern algorithms. Anything security-related uses secure randomness: a seeded gameplay RNG is fine for gameplay, never for tokens.
- **LLM and agent risks** (if the product uses an LLM): prompt injection through user or retrieved content, abuse of tools and functions, data exfiltration through outputs or links, system-prompt leakage, excessive agency. Treat model output as untrusted input.
- **Error handling and logging:** no stack traces or internals shown to users; security events logged and auditable.

## Known quirks (not bugs)
- <grows over time, e.g. "the test hook only exists when the test runner sets its flag">

## Report
Findings only, most severe first:

### [SEC-###] Short title
- **Area:** security
- **Severity:** critical | high | medium | low (roughly CVSS-style: exploitability × impact)
- **Evidence:** <file:line with the data flow, or a failing test, scan output, or request and response against the local app>
- **Impact:** 1–5 (critical = 5, high = 4, medium = 3, low = 1–2)   **Dev effort:** 1–5
- **Proposal:** the fix, plus a regression test that would catch it

End with:
- a threat-model delta: new assets, entry points or threats to add to `docs/THREAT_MODEL.md`
- anything that needs the user's decision, such as accepting a risk or a compliance question

No evidence, no finding. Flag critical and high findings to the user straight away, as well as adding them to the backlog.
```

For a change review rather than an audit, the same agent reviews only the diff and its data flows, and reports blockers first.

## Appendix D: `triage` rules

```markdown
---
name: triage
description: Turns audit and review findings into BACKLOG.md. Discards findings without evidence, merges duplicates, scores priority, assigns Tier and Est. time, and groups Ready items into batches. Use at the end of /audit and /iterate.
model: sonnet
effort: medium
maxTurns: 30
tools: Read, Edit, Write, Grep, Glob
---

Rules:
1. Discard findings with no concrete evidence. List them at the end of your reply, not in the backlog.
2. Merge duplicates: keep the clearest title, combine the evidence.
3. Priority = impact ÷ effort (two decimals). Break ties with broken or risky behaviour first, then smaller changes. **Critical and high security findings go to the top of Ready regardless of score, as a `security` batch (or `solo`), and are flagged to the user.**
4. Check the pillars and the security rules in CLAUDE.md. A finding that breaks either goes to Rejected, with the reason.
5. Preserve status. Never delete or reorder In progress, Done or Rejected. A finding that matches a Ready item updates its evidence. A finding that matches a Done item is a regression: add it as new, with a note. Done items are in BACKLOG_DONE.md: search it, don't read it whole.
6. IDs are AREA-###, numbered after the highest existing number for that area.
7. Sort Ready by priority. One line per row, with evidence as short pointers. Work in one pass: read BACKLOG.md once, then a few Edits or a single Write, without re-reading it to check.
7a. Usage findings (USAGE-###, from the usage report) go in the tooling category. One that would change an agent's model or effort trades quality for usage: mark it as needing the user's decision and keep it out of every batch until they decide.
8. Tier:
   - Deep: <the project's hardest class of work>, A/B experiments, irreversible changes
   - Opus: core, perf or security area, the logic of the safety gates (hooks, the build, the test runner's pass/fail logic), or effort 2 or more
   - Light: everything else at effort 1
   Est. time: Light effort 1, 10–20 min; Opus effort 1, 15–25; effort 2, 20–35; effort 3 or Deep, 35–90+.
9. Batches. Regroup ALL Ready items on every run. B1 is the batch that holds the top item.
   - One category (ui / tooling / core / perf / security / infra / data / solo) and one tier per batch.
   - Items that edit the same function stay apart. Respect dependencies.
   - Caps: ui and tooling 15 (about 5 for effort-2 features); core, perf, security and infra 5; data 3; solo 1.
   - A batch's Est. time is its largest item's estimate, plus 2–3 min per extra ui or tooling item (about 5 min per extra item in the other categories).
   - Self-check before finishing:
     - every item in a batch has the batch's tier
     - no effort-3, Deep, A/B or irreversible item sits outside solo
     - every batch is within its cap
     - nothing in ui or tooling touches core, security or data (watch for refactors or renames of identifiers they use)
     - any item left on its own has been checked against the other batches of its category and tier
Finish with: findings received, kept, merged and discarded; the top 5 Ready items; and the batches (ID, category, item count, Est. time).
```

---

## Appendix E: `BACKLOG.md` template

```markdown
# Backlog

_Last audit: YYYY-MM-DD_

<Note on the columns: Tier, Est. time and Batch. See docs/DEV_CYCLE.md.>

## Batches (regenerated by triage; /iterate runs one batch at a time)
| Batch | Category | Tier | Items | Reviews | Est. time | Why grouped |
|-------|----------|------|-------|---------|-----------|-------------|

## Ready (sorted by priority)
| ID | Area | Title | Impact | Effort | Priority | Batch | Tier | Est. time | Evidence |
|----|------|-------|--------|--------|----------|-------|------|-----------|----------|

## In progress
| ID | Area | Title | Impact | Effort | Priority | Batch | Tier | Est. time | Evidence | Started |
|----|------|-------|--------|--------|----------|-------|------|-----------|----------|---------|

## Done
Shipped items live in BACKLOG_DONE.md.

## Rejected / won't do
| ID | Title | Reason |
|----|-------|--------|
```

`BACKLOG_DONE.md`:

```markdown
# Backlog: Done

## Done
| ID | Title | Tier | Actual time | Result (metric delta / notes) | Commit / PR |
|----|-------|------|-------------|-------------------------------|-------------|
```

---

## Appendix F: Skill skeletons

```markdown
---
name: iterate
description: Take the next batch of Ready items from <Project>'s BACKLOG.md (or a batch, item or items the user names) through the full pipeline once: implement, test, benchmark, review, triage, then ship per the project's git flow. Use when the user says iterate, "do the next batch", or names a batch or item ID.
argument-hint: "[batch ID, item ID, 'ID solo', or nothing]"
---
```

```markdown
---
name: autoiterate
description: Keep running <Project>'s /iterate cycle batch after batch without waiting for the user - intake new requests between batches, ship each batch through the full pipeline, and pause and resume on its own around session limits - until the backlog is done or something needs the user's decision. Use when the user says autoiterate, "keep iterating" or "work through the backlog". For a single cycle, use /iterate.
argument-hint: "[optional: 'stop', 'stop now', N batches, or 'until <ID>']"
---
```

```markdown
---
name: audit
description: Audit the whole of <Project>: run the tests, benchmarks, scans and usage report, dispatch the specialist reviewers in parallel, triage their findings into BACKLOG.md, and summarise the top five items and the batches. Use when the user asks for an audit, a backlog refresh or "what should we improve next".
---
```

The bodies follow Phase 12 step by step, including intake, the reviews-by-category table, the git flow for the profile, the limit rule and the report format. `/autoiterate` points at `/iterate`'s steps rather than copying them, so the two can't drift. Don't set `effort` in any of these skills.

---

## Appendix G: Hook templates

`.claude/settings.json` (merge with the existing keys):

```json
{
  "model": "sonnet",
  "hooks": {
    "PreToolUse": [
      { "matcher": "Bash|PowerShell",
        "hooks": [{ "type": "command", "command": "python \"$CLAUDE_PROJECT_DIR/.claude/hooks/before_push.py\"", "timeout": 900 }] },
      { "matcher": "Edit|Write|MultiEdit",
        "hooks": [{ "type": "command", "command": "python \"$CLAUDE_PROJECT_DIR/.claude/hooks/protect_baseline.py\"", "timeout": 10 }] }
    ],
    "PostToolUse": [
      { "matcher": "Edit|Write|MultiEdit",
        "hooks": [{ "type": "command", "command": "python \"$CLAUDE_PROJECT_DIR/.claude/hooks/after_edit.py\"", "timeout": 60 }] }
    ]
  }
}
```

`before_push.py` (the core; adapt the test and bench commands to the stack):

```python
import json, os, re, subprocess, sys
from pathlib import Path
ROOT = Path(__file__).resolve().parents[2]
# A real `git push`, allowing global options (git -C dir push), but not `git stash push` or "push" in a message
PUSH = re.compile(r"\bgit(?:\s+(?:-C\s+\S+|-c\s+\S+|--?[\w-]+(?:=\S+)?))*\s+push\b")
GIT_C = re.compile(r"\bgit\s+(?:-c\s+\S+\s+)*-C\s+(\"[^\"]+\"|'[^']+'|\S+)")
CD = re.compile(r"^\s*(?:cd|pushd|Set-Location|sl)(?:\s+-(?:Path|LiteralPath))?\s+(\"[^\"]+\"|'[^']+'|\S+)\s*$", re.I)

def to_native(path):  # Git Bash /e/dir -> E:/dir on Windows
    path = path.strip("\"'")
    m = re.match(r"^/([a-zA-Z])(/.*)?$", path)
    if os.name == "nt" and m: path = m.group(1).upper() + ":" + (m.group(2) or "/")
    return os.path.expanduser(path)

def repo_root(d):
    try: out = subprocess.run(["git", "-C", d, "rev-parse", "--show-toplevel"], capture_output=True, text=True, timeout=10)
    except (OSError, subprocess.SubprocessError): return None
    return Path(out.stdout.strip()).resolve() if out.returncode == 0 and out.stdout.strip() else None

def pushes_this_repo(command, cwd):
    """True if any push targets this repo, or its target can't be determined (fail safe)."""
    here = cwd
    for seg in re.split(r"&&|\|\||;|\n", command):
        if (cd := CD.match(seg)): here = str(Path(here, to_native(cd.group(1)))); continue
        if PUSH.search(seg):
            c = GIT_C.search(seg)
            target = str(Path(here, to_native(c.group(1)))) if c else here
            root = repo_root(target) if Path(target).is_dir() else None
            if root is None or root == ROOT.resolve(): return True
    return False

def run(args):
    p = subprocess.run([sys.executable, *args], cwd=ROOT, capture_output=True, text=True, encoding="utf-8", errors="replace")
    return p.returncode, p.stdout + p.stderr

def main():
    try: data = json.load(sys.stdin)
    except ValueError: return 0
    command = str((data.get("tool_input") or {}).get("command", ""))
    if not PUSH.search(command) or not pushes_this_repo(command, data.get("cwd") or os.getcwd()): return 0
    code, out = run([str(ROOT / "tests" / "run_tests.py")])
    if code: sys.stderr.write("Push blocked: tests failed.\n" + out[-2000:]); return 2
    code, out = run([str(ROOT / "bench" / "run_bench.py"), "--compare"])
    if code: sys.stderr.write("Push blocked: benchmark regression or broken budget.\n" + out[-2500:]); return 2
    return 0

sys.exit(main())
```

Test the targeting logic from a file, not from a command line that contains the push text itself (that would fire the gate). Cover: a plain push, `cd <this repo> &&`, `git -C <this repo>`, `cd <other repo> &&`, Windows and Git Bash paths, and an unknown directory, which must be gated.

`protect_baseline.py` denies with:

```json
{"hookSpecificOutput": {"hookEventName": "PreToolUse", "permissionDecision": "deny",
  "permissionDecisionReason": "bench/baseline.json only changes through the --baseline command, and only when the numbers genuinely improved."}}
```

`after_edit.py`: if `tool_input.file_path` ends with a watched path, run the formatter and linter on that file (if the project has them), then the tests with `--quick`, and exit 2 with the tail of the output on failure.

For Team and Enterprise, add a CI workflow (for example GitHub Actions) that runs the same test and bench-compare commands on every PR, and make them required status checks.

---

## Appendix H: Lessons from the reference build

1. **Simple stacks compound, but a starting choice isn't a rule.** No build step and no dependencies made testing, deploying, reviewing and publishing trivial. But "everything in one HTML file" was written into the project's docs and agent instructions as a rule, and one reviewer was told to reject any proposal to split the file. The owner had never asked for it. By the time the simulation grew, it cost real time: a 2,800-line file every agent had to page through, `file:line` evidence going stale after every batch, and two implementers unable to share one working tree. It was dropped. The only real requirement was that the game stays playable at its hosted URL. Record stack choices as decisions with a reason and a revisit trigger, and add complexity only when a requirement forces it, at any scale.
2. **Design for determinism and inspection first.** Seeded randomness, fixed steps and a test hook are what make invariant tests, benchmarks and reproducible bug reports possible.
3. **Evidence or it didn't happen.** Requiring a metric, a screenshot or a `file:line` for every finding kept a first audit of 49 findings actionable.
4. **Reviews catch what tests miss.** The code review caught a scope-creep regression, and a fragile check that only worked by coincidence at startup. Make implementers list behaviour changes beyond an item's scope.
5. **Benchmarks drift on laptops.** Use medians of interleaved runs, stash/pop A/B tests and stable CI runners. Re-baseline only on a real improvement or a change of measuring method, and ask before re-baselining for machine drift. In the reference project, the fastest of 3 back-to-back runs swung 10-30% between identical runs. The median of 5 interleaved runs agreed within ±3%.
6. **Re-measure backlog claims.** A claimed 64 → 37 ms speed-up didn't reproduce. Record "benefit unverified" rather than repeating the claim.
7. **Cheap models for checklists, strong models for hard reasoning.** Tiered implementers and reviewers pinned explicitly to Opus kept quality where it mattered and cut cost everywhere else.
8. **Low effort isn't free when judgment matters.** Triage at low effort broke its own batching rules. Medium effort, a self-check and a mechanical verification script fixed it.
9. **Batch independent work.** 14 small fixes shipped in about 35 minutes, not an estimated 3–4 hours. Group by category and tier, cap the batch size, and scope reviews to the category.
10. **Never let two agents edit the same working tree.** One implementer per batch. Real parallel work needs git worktrees.
11. **Check bulk edits mechanically.** One table edit silently dropped 12 rows. A 20-line check script catches that instantly.
12. **Tools fail, so plan the fallback.** The browser MCP failed to connect several times. The agents fell back to scripted Playwright and screenshots, and said so.
13. **Docs move together.** Every workflow change updates `CLAUDE.md`, `DEV_CYCLE.md`, the skills, the agents and CI in one change, or they drift apart.
14. **Report the way the user reads.** The reference user wanted every iteration reported as a table (ID, Tier, Est. time, change), followed by actual against estimated time. Ask early how reports should look, and build that into the skills.
15. **Scope the gates to their repository.** The reference project's push gate fired on every push the session ran, so publishing a separate repo (this spec) was blocked by the game's benchmark noise. A gate must check which repo a command targets, and still fail safe when it can't tell.
16. **Consolidate requests as they arrive.** The reference user sent ideas in bursts while agents were working: a bug, a progression system, a save system, a font theme, scene ideas. Taken one by one they would have produced near-duplicate items (a save system, a snapshot link, an undo and a share link all needed the same serializer). An intake step merged them into existing items, retired two as superseded, and had triage re-order the queue while keeping the user's pins. Run it at the start of every cycle, not just at audits.
17. **Separate one cycle from the loop, and check the clock on limits.** "Keep iterating while I'm away" and "do the next batch" are different commands: `/iterate` for one cycle, `/autoiterate` for the loop. During the reference build an agent failed with "session limit, resets 2pm"; the orchestrator treated it as live and rerouted work, but it was already 4:45pm and the limit had long reset. Late notifications are normal, so read the real clock before pausing.
18. **Documentation is more than developer docs.** The reference build had a strong README, architecture guide and dev-cycle guide, but no user guide and no reference for the rules the simulation follows. Those rules were scattered across code comments and backlog entries, so every reviewer re-derived them. The owner noticed only after dozens of batches. Plan the user guide, the domain-logic reference and the operations notes from launch, give one agent the job of keeping them true, and audit docs like code.
19. **Measure where the usage goes, and fix the shape before the model.** The reference user, on a usage-limited plan, was hitting the session limit a couple of hours into each session. Pricing every transcript by model showed where it went: the orchestrating main session was 47% of all usage (one session ran for a day and a half without compacting and reached about 740K tokens, all of it re-read on every turn); the one A/B experiment was 21%, most of it spent re-caching the implementers' whole context after idle gaps longer than the five-minute cache; two `/code-review high` runs were 7%; and reviewers re-ran the test suite the orchestrator had just run. The model mix wasn't the problem: cached re-reads cost the same on Opus and Sonnet, and the Opus reviews took 6-15 turns where the Sonnet ones took 24-71. The fixes were a compaction window, a one-hour cache for implementers, named review levels, briefing reviewers with the results, one triage call per batch, and a script that repeats the analysis in every audit without spending model tokens.
20. **A weaker first pass can cost more than it saves.** A Sonnet-tier fix to the reference project's push gate needed two review rounds, which found 5 and then 10 real bugs, each round meaning more implementation and another review. Output tokens, which effort mostly changes, were under a tenth of usage; the cost is in turns and context. Judge settings by cost per finished task, and send the logic of safety gates to the stronger tier.
21. **Decide the usage ethos at the start.** The reference project was launched without asking how much its owner could spend, so it was tuned for thoroughness: Opus reviewers, full audits, high-level code reviews, A/B experiments. Its owner was on a usage-limited plan, hit the session limit a couple of hours into each session, and the retuning came only after two days of work. Asking in Phase 1, and setting a usage profile that shapes the agents, the workflow and the code layout from the first commit, would have avoided that.
