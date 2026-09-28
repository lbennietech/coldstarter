# Coldstarter

*From idea to a self-improving project: the project-launch uber-prompt for Claude Code.*

Copyright (c) 2026 Luke Bennie <lukebennie@gmail.com>. Licensed under CC BY-NC 4.0 (see `LICENSE`).

| | |
|---|---|
| **Author** | Luke Bennie ([lukebennie@gmail.com](mailto:lukebennie@gmail.com)) |
| **Version** | 1.0.5 (2026-09-28) |
| **Origin** | Designed by Luke Bennie while building Pocket Universe, a browser gravity sandbox, from idea to self-improving dev loop over 2026-09-27/28, with Claude Code (Anthropic's Claude Opus 5.5 and Sonnet 5) as the implementing collaborator. The development method it encodes came from Luke's direction: the audit and iterate loops, tiered model routing for token efficiency, batch streamlining, time-tracked reporting, the dedicated security reviewer, and generalising it for any project at any scale. |

### Version history

| Version | Date | Changes |
|---|---|---|
| 1.0.5 | 2026-09-28 | Public release under CC BY-NC 4.0 (previously all rights reserved). Removed the link to the reference project's repository. No changes to the method. |
| 1.0.4 | 2026-09-28 | Corrections from a code review of the reference implementation: memory growth is also a median (the worst run measures warm-up, not steady growth). `--baseline` checks the method recorded in the result it's saving, and refuses partial runs. Mismatch messages say whether to re-run or re-baseline. A cheap single-run mode for agents. Medians fix noise within a session, not drift between sessions, so the robust gate compares against the committed code in the same session. Gate hooks need generous timeouts, because a timed-out hook doesn't block. |
| 1.0.3 | 2026-09-28 | Benchmarks: interleave repeated runs across scenarios, take the worst run for memory growth, stamp the measuring method into results and baselines, and refuse mismatched comparisons. Lesson 5 updated with the measured result (±3% against 10-30% swings). |
| 1.0.2 | 2026-09-28 | Fix: the push-gate hook only gates pushes of its own repository (it follows `cd`/`Set-Location` and `git -C`, and fails safe when unsure). The old template gated every push made from the session, including other repos. Lesson 15 added. |
| 1.0.1 | 2026-09-28 | Renamed from Launchframe to Coldstarter (file `COLDSTARTER.md`, repo `lbennietech/coldstarter`). No changes to the method. |
| 1.0 | 2026-09-28 | First release, as Launchframe: scale profiles (Solo/Team/Enterprise), project types, 17 launch phases, core agent roster with a dedicated security reviewer, triage and batching engine, hooks and CI, model routing, lessons from the reference build. |

> **What this is.** A general launch pad for any serious project, for business or pleasure, solo or enterprise. You give it an idea, a business problem, a question to answer, a product or tool to build, or an integration to set up. It turns that into a working first version and a self-improving development framework. The framework includes:
>
> - a repository with a CI/CD flow to match
> - tests and quality gates
> - performance and correctness measurement
> - specialist reviewer agents
> - an evidence-based backlog with a triage and batching engine
> - `/audit` and `/iterate` loops
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
> Claude interviews you, proposes a solution, a technology stack and a scale profile, builds v1, then sets up the whole framework, stopping at key decision points. When it's done, you run `/iterate`.
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
9. **Respect the token budget.** Ask about it in Phase 1, and default to the cheapest setup that does the job well (Phase 13).
10. **Write docs for a person who starts cold.** Plain, direct sentences, tables for reference material, no filler.

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
| Docs | README, ARCHITECTURE, DEV_CYCLE | Plus decision records in `docs/adr/`, and CONTRIBUTING | Plus a threat model, runbooks, SLOs, a data classification, onboarding docs and a changelog |
| Operations | None, or console logging | Error tracking and basic metrics | Logging, metrics, tracing, SLOs, alerting, and incident and rollback runbooks |
| Compliance | None | Privacy basics (GDPR/CCPA if personal data) | Whatever applies: SOC 2, ISO 27001, GDPR, HIPAA, PCI DSS, accessibility law. Plus an audit trail. |
| Agent autonomy | High: implements, commits and pushes | Medium: implements and opens PRs; people merge | Low to medium: implements and opens PRs; people approve. No production access. Tools restricted. |
| Audit cadence | Rarely | Each milestone | Scheduled, with CI-driven checks in between |

Mixed cases are normal. For example, a solo developer building something that handles payments uses Solo git flow but Enterprise-grade secrets handling and security review depth. Record every deviation in the Decisions log.

---

## Phase 1: Intake and problem analysis 🚦

**Goal:** an agreed problem statement, project type and scale profile, before any solutioning.

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
   - **Usage plan:** the Claude plan (Pro, Max, Team, Enterprise) and whether token usage is a concern.
   - **Reporting:** how the user wants progress reported (tables, summaries, which columns).
   - **v1 scope:** the smallest version that would be worth having.
4. Write back a **problem analysis** of about one page:
   - the problem restated
   - the users or stakeholders and their jobs-to-be-done
   - success criteria (measurable)
   - constraints
   - risks and unknowns
   - what's out of scope for v1
   - the **recommended scale profile** and **project type**, with reasons

   If a non-software answer is better, say so here (ground rule 2).
5. 🚦 **Gate:** the user confirms or corrects the problem analysis, the scale profile and the project type.

---

## Phase 2: Solution design and tech proposal 🚦

1. Propose **2 or 3 solution shapes**, each with its main trade-off, and recommend one. Cover the stack, hosting, data storage, and how it integrates with existing systems.
2. Choose by these principles, in order:
   - **Fewest moving parts that meet the requirements, including the non-functional ones** (security, availability, compliance, scale). The reference project was one HTML file with no build and no dependencies, and that paid off everywhere. At enterprise scale, "fewest moving parts" still applies within the organisation's mandated platforms.
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
3. Set the **header convention** for new source files (copyright or licence line) and record it in `CLAUDE.md`.
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

1. Build the v1 features in the chosen stack. Keep the structure as simple as the stack allows.
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
3. Write a **threat model** in `docs/THREAT_MODEL.md`: assets, actors, trust boundaries, entry points, top threats, mitigations. For Solo, half a page is enough, since even a static site has third-party scripts, user input and a deploy pipeline. For Team and Enterprise, add a **data classification** for every data store. The security reviewer keeps it current.
4. Commit.

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
- **Conventions:** commit authorship, header, code style, platform and input conventions, "keep README in step", "all randomness through `rand()`".

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
| **code-quality reviewer** | The whole codebase, not a diff: coupling, duplication, error handling, gaps in test coverage. Respects the stack decision. | Sonnet / medium |
| **security reviewer** | A dedicated security specialist, on **every project and every audit**, and on any change that touches a sensitive area. Covers the threat model, authentication and authorisation, input handling and injection, secrets, dependencies and supply chain, headers and CSP, CI/CD and infrastructure config, data protection, and LLM-specific risks. Full spec in Appendix C2. | Opus / medium |
| **user tester** | Runs the tests with screenshots, then uses the product live as **three personas**: *newcomer* (the first 60 seconds, arriving cold), *power user* (builds something deliberate), *breaker* (spams input, extreme values, resizing, switching mid-action). Gives a ship verdict or audit findings. | Sonnet / low |

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
- **Area:** perf | core | ux | design | efficiency | code | security | infra | data | <domain>
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
| `implementer-opus` | Opus / medium | **Opus tier:** core-logic, perf or security items, or anything at effort 2+ |
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

1. Create `BACKLOG.md` (template in Appendix E) with these sections: **Batches, Ready, In progress, Done, Rejected**.
   - **Ready** columns: ID, Area, Title, Impact, Effort, Priority, Batch, Tier, Est. time, Evidence.
   - **Done** columns: ID, Title, Tier, Actual time, Result (metric delta or notes), Commit or PR.
2. **Triage rules:**
   - discard findings without evidence, and merge duplicates
   - priority = impact ÷ effort
   - a finding that breaks a pillar goes to Rejected
   - never reorder In progress, Done or Rejected
   - a finding that matches a Done item is a regression
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

## Phase 12: Skills: `/audit` and `/iterate`

Create `.claude/skills/audit/SKILL.md` and `.claude/skills/iterate/SKILL.md` (skeletons in Appendix F). Don't set `effort` in skill frontmatter, so the session's `/effort` still applies.

**`/audit`**: rare and expensive; the backlog's source of truth.
1. Check the build is current and the tools work.
2. Collect evidence: the tests with `--screens`, `bench --compare`, and the security scans (Team and Enterprise).
3. Dispatch every relevant reviewer **in parallel, in the background**, always including the security reviewer. Brief each with the key numbers and the evidence paths. They're read-only and cite their evidence. If the project's agents aren't available as agent types, run general-purpose agents told to follow the matching agent file.
4. `triage` merges the findings and scores them, assigns Tier and Est. time, and regroups the batches.
5. Report: the headline numbers, the top 5 Ready items, the batches, and the decisions the user needs to make. Commit `BACKLOG.md` (in a PR, for Team and Enterprise).

**`/iterate`**: everyday, and batch-first.

| Command | Does |
|---|---|
| `/iterate` | Run `B1`, the batch that holds the top Ready item |
| `/iterate B3` | Run a named batch |
| `/iterate ITEM-ID` | Run the batch that contains that item |
| `/iterate ITEM-ID solo` | Run just that one item |
| `/iterate B2 without ID` | Run a batch minus some items |
| `/iterate B3 on Opus` | Raise a batch's tier |

1. **Pick.** State the batch, its category, tier, items, reviews and Est. time. Move the items to In progress. Regroup first if the batches are stale. Team and Enterprise: create a branch named `batch/<id>-<slug>`.
2. **Implement.** Brief **one** implementer of the batch's tier with every item's row and proposal. It works through them as separate, isolated edits and runs the tests once at the end. **Never run two implementers on the same working tree**: they overwrite each other's uncommitted edits. Parallel work needs separate git worktrees. An item that turns out riskier than its category goes back to Ready.
3. **Test.** The full tests pass. If `bench --compare` fails, run the stash/pop A/B test to tell a real regression from machine noise.
4. **Review.** Run the category's reviews once, briefing each reviewer with the whole item list.
5. **Triage.**
   - Blockers go back to **the same implementer via SendMessage**, so it keeps its context. If that agent has already finished, start a fresh one and give it the full context.
   - Re-run steps 3–4 after the fix.
   - Drop an item from the batch rather than hold up the rest.
   - Everything else goes to the backlog.
6. **Ratchet the baseline** only after a genuine improvement.
7. **Finish.**
   - Give each item its own Done row. Record the batch's actual time once, on its first item.
   - Commit per item when the diffs separate cleanly. Otherwise, make one commit that lists every ID.
   - **Solo:** push (the hook gates the push), then deploy or republish if the product changed.
   - **Team and Enterprise:** open a PR with the batch table and test and bench results, let CI run, and hand it to the reviewers. Merge only if the profile allows it; Enterprise never self-merges.
   - Have `triage` regroup the remaining items.
   - **Report** to the user as a table (ID, Tier, Est. time, short description of the change), then actual against estimated time, the test and bench numbers, the PR link if any, and the next batch.

Commit.

---

## Phase 13: Model routing and token efficiency 🚦 (confirm with the user)

Apply this policy unless the user says otherwise: *use the strongest model only where it's clearly better, and send routine or checklist work to cheaper models or lower effort.*

| Setting | Default |
|---|---|
| Session default | Sonnet at **medium**, pinned in `.claude/settings.json` (`"model": "sonnet"`) |
| `/effort high` | In the main session, only for hard reasoning, then back to medium. Avoid xhigh and max. |
| Opus, medium | domain-correctness, perf, security and evaluation reviewers, and `implementer-opus`. Name `model: opus` explicitly (not `inherit`) so they stay on Opus in a Sonnet session. |
| Opus, high | `implementer-deep` only |
| Sonnet, medium | UX, product, code-quality, compliance, infra and data reviewers, `implementer`, `triage` |
| Sonnet, low | efficiency auditor, user tester, accessibility reviewer |
| Haiku | Only where it's proven good enough (for example mechanical formatting or lookups), after a trial |

- Change agent settings **one level at a time**, with a reason, and record the date and reason in `CLAUDE.md`.
- Switch models at the **start** of a session, because prompt caching is per model.
- Let batching save the cost: one cycle per batch, not per item. Run `/audit` rarely.
- Don't spawn agents for work that takes a couple of direct tool calls.
- Durable project preferences go in the checked-in docs. Personal preferences go in memory.

Commit `CLAUDE.md` and the agent frontmatter.

---

## Phase 14: Hooks and CI: deterministic enforcement

**Local hooks** (all profiles): scripts in `.claude/hooks/`, wired in `.claude/settings.json` (templates in Appendix G).

| Hook | Event | What it does |
|---|---|---|
| `after_edit.py` | PostToolUse on Edit/Write/MultiEdit | If a watched file changed (source, test harness, bench scenarios), runs the **quick** check. Exits 2 with the output on failure. |
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
4. **Retrospective with the user.** Look at actual against estimated time, token spend, which agents found real problems and which produced noise, and whether the scale profile still fits. Tune the model and effort settings and the batch caps **one level at a time**, and record every change and its reason in `CLAUDE.md`.
5. Commit.

---

## Phase 17: Handoff

1. Write `docs/DEV_CYCLE.md`, the human guide. It covers:
   - a table of the commands
   - the backlog columns (Tier, estimated and actual time)
   - the batch categories and why the caps exist
   - the steps of `/iterate` and `/audit`
   - the git and PR flow for this profile
   - how to choose what to iterate on (by theme, cost, dependencies, fixes before features)
   - the A/B experiment convention
   - how to keep usage down
   - three worked examples using real batches from the backlog
2. Make sure `CLAUDE.md`, `DEV_CYCLE.md`, `ARCHITECTURE.md`, the skills, the agents, CI, the runbooks and `README.md` all agree. **Every workflow change updates all of them in the same commit or PR.**
3. Tick every phase in `docs/PROJECT_PROGRESS.md`. Once the development loop is live, mark it and this spec as archived history.
4. Give the user a brief summary of the dev loop, with its commands and the first batch to run.

### Definition of done

Items marked (T) apply to the Team profile, (E) to Enterprise, and (T/E) to both.

- [ ] Problem analysis, project type, scale profile, stack, non-functional requirements and pillars confirmed (in the Decisions log)
- [ ] Repository with author identity, `.gitignore`, licence and header convention; remote, CI and deployment as agreed; branch protection and CODEOWNERS (T/E); environments as infrastructure-as-code (E)
- [ ] A working v1, shown to the user
- [ ] `docs/ARCHITECTURE.md` accurate; the system is deterministic, steppable and inspectable from tests; threat model (all profiles) and data classification (T/E)
- [ ] `CLAUDE.md` with files, the ship routine, numeric targets, invariants, security and compliance, pillars, workflow, model and effort, conventions
- [ ] Tests (full, quick and screens), the cross-platform matrix, invariant tests and accessibility checks all pass; security and dependency scans (T/E)
- [ ] Benchmarks with a median-of-N baseline, a 5% compare gate and a budget report
- [ ] Environment helper; MCP with a fallback; allowed-tool list (E)
- [ ] Read-only reviewers with evidence rules and quirk lists, **including the security reviewer**; the three implementer tiers; triage
- [ ] The first audit includes a security baseline; secret and dependency scans pass
- [ ] `BACKLOG.md` with the Batches, Tier, estimated and actual time columns, plus a mechanical consistency check
- [ ] `/audit` and `/iterate` working end to end, batch-first, following the profile's git flow
- [ ] Hooks (quick check, push gate, baseline guard, secrets guard); CI mirrors them (T/E)
- [ ] Observability, SLOs, runbooks and cost alerts (T/E, hosted services)
- [ ] First audit, first batch shipped, retrospective done, settings tuned
- [ ] `docs/DEV_CYCLE.md` written, all docs consistent, summary given to the user

---

## Appendix A: Adapting the framework by project type

| Project type | Suggested stack (lightest first) | Deploy | Test tooling | Domain-correctness reviewer checks | Invariants | Benchmarks |
|---|---|---|---|---|---|---|
| **Consumer web app / site** | Static site or single file; SvelteKit/Next.js + SQLite/Postgres | GitHub Pages, Vercel, Netlify | Playwright E2E (3 engines + devices), unit tests | business rules, state handling | no data loss; forms validate; auth enforced | Core Web Vitals, bundle size, p95 latency |
| **Business SaaS / internal tool** | The organisation's standard web stack + Postgres; SSO | Organisation cloud, Fly.io, Render | Unit, API contract, E2E, axe-core | business rules, authorisation, money maths, audit trail | ledgers balance; permissions enforced; idempotent writes; audit log complete | p95 latency at target load, queries per request, cost per user |
| **Enterprise integration / middleware** | The organisation's integration platform or a small service + a queue | Organisation cloud, infrastructure-as-code | Contract tests against system fakes, replayed messages | mappings, retries, ordering, failure handling | exactly-once or at-least-once holds; nothing lost or duplicated; reconciliations match | throughput, end-to-end latency, backlog drain time |
| **Microservices / platform** | Only if a monolith truly won't do; containers + infrastructure-as-code | Kubernetes, ECS, Cloud Run | Contract, integration and chaos tests | API compatibility, consistency, resilience | backward compatibility; consistency rules; graceful degradation | p95/p99 per service, error rate, cost |
| **Data / analytics pipeline** | Python + DuckDB/Polars; dbt for the warehouse | Scheduled jobs, Airflow, GitHub Actions | pytest with fixture datasets, data tests | joins, deduplication, schema drift, lineage | row counts reconcile; schema matches; reruns idempotent; reproducible | runtime and peak memory at 1× and 10× |
| **Analysis / business question** | Notebooks + DuckDB + a report or dashboard | Report, dashboard, slide deck | Reproducible notebook runs, data tests | methodology, statistics, bias, assumptions | the same data gives the same numbers; sanity checks hold | runtime, freshness |
| **AI / LLM application** | Claude API (latest model) + a thin backend | Vercel, Fly.io, organisation cloud | **An evaluation harness** as the invariant suite | prompt quality, grounding, tool use, refusals, safety | eval pass rate ≥ target; structured output validates; no leaked secrets or personal data | cost per task, p95 latency, cache hit rate |
| **Mobile app** | PWA first; then React Native/Flutter | Web, app stores | Device emulation, then device farms | offline behaviour, state restore, permissions | no data loss across restarts or offline | startup time, frame time, battery and network |
| **CLI / library / SDK** | Python, Go, Rust, TypeScript | PyPI, npm, crates, releases | Unit and golden-file tests on each OS | API contract, backward compatibility | documented behaviour holds; round-trips; no crash on bad input | throughput, startup, size |
| **Game / simulation** (the reference project) | Single file with Canvas/WebGL; or an engine | itch.io, Pages, stores | Playwright or engine test runner, invariant tests | numerical stability, conservation, determinism | same seed gives the same state; drift within tolerance; no NaN or tunnelling | fps per scenario, slow-device profile, memory |
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
- <Phase 13 routing table, with dates and reasons>
## Conventions
- Header: `Copyright (c) <year> <Owner>. All rights reserved.` (or the licence line)
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
tools: Bash, Read, Glob, Grep   # + mcp__playwright for UI roles; never Edit/Write
---

You review <facet> of <Project>. Read `CLAUDE.md` (targets, pillars, security) and `docs/ARCHITECTURE.md` first.
You never edit, commit, push or deploy, and you never use production credentials. Throwaway experiments go in a temp folder outside the repo.

## Measure first
<commands that produce evidence: tests --screens, bench scenarios, scans, test hooks>

## Look for
<domain checklist>

## Known quirks (not bugs)
- <grows over time>

## Report
Findings only, most valuable first, in the AREA-### format. No evidence, no finding.
(When reviewing a change rather than auditing: findings with file:line, a concrete trigger scenario and your confidence. Say plainly if nothing is worth fixing.)
```

**Implementer** (three copies: `implementer` sonnet/medium, `implementer-opus` opus/medium, `implementer-deep` opus/high):

```markdown
---
name: implementer
description: Implements a <Project> BACKLOG.md batch (or a single item) as the smallest reasonable changes, one isolated edit per item. <Tier scope>. Use from /iterate.
model: sonnet
effort: medium
tools: Bash, Read, Edit, Write, Glob, Grep
---

You implement backlog items for <Project>. Read `CLAUDE.md` first.
You implement and test. You never commit, push, merge or deploy.

## What to do
1. Make the smallest reasonable change that delivers each item, following the project's conventions and security rules.
2. Add or extend a test for new behaviour.
3. Run the full tests once at the end. If something fails, fix it or report exactly what's blocking. Don't work around it.
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
tools: Read, Edit, Write, Grep, Glob
---

Rules:
1. Discard findings with no concrete evidence. List them at the end of your reply, not in the backlog.
2. Merge duplicates: keep the clearest title, combine the evidence.
3. Priority = impact ÷ effort (two decimals). Break ties with broken or risky behaviour first, then smaller changes. **Critical and high security findings go to the top of Ready regardless of score, as a `security` batch (or `solo`), and are flagged to the user.**
4. Check the pillars and the security rules in CLAUDE.md. A finding that breaks either goes to Rejected, with the reason.
5. Preserve status. Never delete or reorder In progress, Done or Rejected. A finding that matches a Ready item updates its evidence. A finding that matches a Done item is a regression: add it as new, with a note.
6. IDs are AREA-###, numbered after the highest existing number for that area.
7. Sort Ready by priority. One line per row, with evidence as short pointers.
8. Tier:
   - Deep: <the project's hardest class of work>, A/B experiments, irreversible changes
   - Opus: core, perf or security area, or effort 2 or more
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
| ID | Title | Tier | Actual time | Result (metric delta / notes) | Commit / PR |
|----|-------|------|-------------|-------------------------------|-------------|

## Rejected / won't do
| ID | Title | Reason |
|----|-------|--------|
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
name: audit
description: Audit the whole of <Project>: run the tests, benchmarks and scans, dispatch the specialist reviewers in parallel, triage their findings into BACKLOG.md, and summarise the top five items and the batches. Use when the user asks for an audit, a backlog refresh or "what should we improve next".
---
```

The bodies follow Phase 12 step by step, including the reviews-by-category table, the git flow for the profile, and the report format. Don't set `effort` in either skill.

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

`after_edit.py`: if `tool_input.file_path` ends with a watched path, run the tests with `--quick` and exit 2 with the tail of the output on failure.

For Team and Enterprise, add a CI workflow (for example GitHub Actions) that runs the same test and bench-compare commands on every PR, and make them required status checks.

---

## Appendix H: Lessons from the reference build

1. **Simple stacks compound.** One file, no build step, no dependencies made testing, deploying, reviewing and publishing trivial. Add complexity only when a requirement forces it, at any scale.
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
