# Coldstarter

*From idea to a self-improving project: the project-launch uber-prompt for AI coding agents (Claude Code is the reference platform).*

Copyright (c) 2026 Luke Bennie <lukebennie@gmail.com>. Licensed under CC BY-NC 4.0 (see `LICENSE`), with one extra permission: projects and products you build with Coldstarter are yours, including commercial ones. Selling, sublicensing or repackaging this spec, or adaptations of it, needs the author's written permission.

| | |
|---|---|
| **Author** | Luke Bennie ([lukebennie@gmail.com](mailto:lukebennie@gmail.com)) |
| **Version** | 2.1.0 (2026-09-30) |
| **Origin** | Designed by Luke Bennie while building Pocket Universe, a browser gravity sandbox, from idea to self-improving dev loop over 2026-09-27/28, with Claude Code (Anthropic's Claude Opus 5.5 and Sonnet 5) as the implementing collaborator. The development method it encodes came from Luke's direction: the audit and iterate loops, tiered model routing for token efficiency, batch streamlining, time-tracked reporting, the dedicated security reviewer, and generalising it for any project at any scale. |

> **Keep this file platform-neutral: a rule for any AI or person using or editing it.** Coldstarter works with any AI coding platform. The phases and templates describe the method in neutral terms: the *agent* (the AI coding agent running the launch), the *project instructions file*, *path-scoped instructions*, *skills* (procedures loaded on demand), *subagents*, *hooks*, *project settings*, the *change review*, the *browser tool*, *strong, standard and light model tiers*, *effort*, *turn caps* and *cache lifetimes*. **Appendix I (Platform bindings)** maps each term to a concrete platform, with Claude Code as the reference binding. When launching a project, use your platform's binding; if it has none, map the terms yourself and record the mapping in the Decisions log. When editing this file, write every rule in neutral terms, and put platform-specific detail (file paths, settings keys, event names, model names, commands, file formats) only in Appendix I. Version control works the same way: the phases speak of *committing*, *publishing*, the *main line*, *branches*, *PRs* (review requests), *setting a change aside* and *isolated working copies*, and **Appendix K (Version control bindings)** maps them to Git (the default), Subversion, snapshots, or none at all.

### Version history

| Version | Date | Changes |
|---|---|---|
| 2.1.0 | 2026-09-30 | **Process has to earn its place, a cheaper reviewer roster, and lexicographic priority.** A new ground rule (12): the product comes first, and every document, agent, skill, hook and gate exists only if it pays for itself on this project. Template sections with nothing project-specific to say are left out, and the user can cut anything from the dev plan (for example *"We're building a $100 internal utility. Don't write architecture documentation unless the complexity warrants it."*); the cut is logged with the trigger that would bring it back. A **process budget** (section 1): intake asks what the project is worth, and the dev plan compares the framework work after v1 with v1's own. For None and Light it must come in well under, and for Standard and Full the plan says what the framework costs and buys. **Light is now a minimum set plus triggers:** README (with the user and developers' guides as sections), readable code, an instructions file of about 50 lines with only the targets the project has, core and end-to-end tests with a secret scan and dependency audit, the security reviewer and the change review, the three Light hooks, `docs/TODO.md`, and one review of v1. Everything else waits for a trigger written into the dev plan: ARCHITECTURE, the domain-logic reference (a data view keeps its metric dictionary), OPERATIONS, the threat model, the design guide, a separate user guide or DEV_CYCLE, invariant and accessibility tests, benchmarks, Phase 9's tools, an implementer agent (the session implements until then), `/devmanual`, a project skill and Phase 13's routing work. For Light, Phase 13's gate folds into Phase 2's, and Phase 6's runs only when there are targets or invariants to confirm. Every retrospective now **prunes** process that isn't used: docs nobody reads, agents whose findings were all noise, skills nobody runs. Existing Light projects can keep what they have; their next retrospective decides what stays. **A cheaper reviewer roster.** Only three reviewers are always on: domain correctness, code quality and security. The rest are conditional, each created only if the project needs the role and dispatched by `/audit` only when its trigger fires: the perf profiler when a budget is missed or a hot path changed; the UX reviewer, user tester and accessibility reviewer when the UI or user-facing behaviour changed; the infra/SRE, data, compliance and evaluation reviewers when their areas changed; the docs writer when a new mechanical **docs drift check** flags a doc; and the efficiency auditor and product designer only in a milestone audit. `/audit` is now the **core audit**, and `/audit full` the **milestone audit** with every reviewer. Step 2 lists what changed since the last audit (recorded on `BACKLOG.md`'s "Last audit" line) so the triggers are checked mechanically, and the report says which reviewers ran and why. The usage profile decides borderline triggers and how often `/audit full` runs, and the retrospective tunes the triggers. **Lexicographic priority.** Impact ÷ effort alone let a cosmetic fix (2 ÷ 1 = 2.0) outrank a security hole (5 ÷ 3 = 1.67). Every backlog item now gets a **priority class** first: P1 safety and security blockers, P2 correctness and data-loss blockers, P3 user-visible regressions, P4 high-impact improvements (including medium and low security findings and conformance gaps), P5 efficiency and polish. Impact ÷ effort orders items only within a class, and the Priority column reads `P1 1.67`. The class comes from the kind of problem and the evidence that class requires (an exploit path, a failing invariant, a before and after), not from the impact score, so an inflated impact can't jump a class; only a user's pin can. Batches are numbered by their highest-priority item, the backlog check verifies the sort, and Light's `docs/TODO.md` follows the same order. |
| 2.0.0 | 2026-09-29 | **Platform-neutral throughout, and version control as a choice.** Every Claude Code specific that was written inline in the phases and templates has moved to **Appendix I**, which is now the full Claude Code binding: a term table (I.1), the project settings with the reference routing and hook wiring (I.2), agent and skill frontmatter (I.3, I.4), hook input and output (I.5) and the usage report's transcript format (I.6). The phases use neutral terms: *the agent*, *the project instructions file* (was `CLAUDE.md`), *project settings*, the *change review* (was `/code-review`), the *browser tool*, *turn caps*, *cache lifetimes*, resuming an agent, and a loop or scheduler. Agent and skill templates (Appendices C, C2, D, F) give their settings as neutral tables (model tier, effort, turn cap, tools as read, search, shell, edit and the browser tool) for each platform to write in its own format. Appendix G's hook scripts mark their two platform bindings (how they read their input and how they block), and the publish-gate script finds its repository through the version-control tool instead of assuming its folder depth. Usage-profile suggestions name plan levels rather than Claude plans. **Breaking:** the backlog's work tiers are renamed **Routine, Hard and Deep** (were Light, Opus and Deep), and `implementer-opus` becomes `implementer-hard`, so work tiers no longer share names with model tiers or framework sizes. Existing projects rename the Tier values in `BACKLOG.md` and the agent file, and the publish-gate script and its hook wiring (below). The reference binding also wires the secrets guard to `MultiEdit`. **Version control is now chosen in Phase 1:** Git (the default), another tool such as Subversion, snapshots, or none at all. The phases speak of committing, publishing, the main line, branches, PRs, setting a change aside and isolated working copies, and a new **Appendix K** binds them for Git, Subversion, snapshots and none, with install commands (the launch checks the tool is installed and asks before installing it). Standard and Full need version control; a None deliverable can use snapshots or nothing at all, and a Light project can with a logged reason. **Breaking:** the push gate is now the publish gate, `before_publish.py` (it gates `git push`, or `svn commit` in Subversion). |
| 1.8.1 | 2026-09-29 | Fixes from a full review of the spec. Framework sizing now reaches everywhere it should: a Light launch runs Phases 10 and 12 for its implementer, security reviewer and `/devmanual`, and has a tests-only push gate (the sizing table, Phase 14 and the definition of done now agree); a None deliverable gets its security pass in the handoff and follows its visual direction without a separate design guide; Phase 6, the `CLAUDE.md` skeleton and the docs rule describe the Light routine; Full means Standard-type projects at Team or Enterprise scale. The readable-code layout rules follow the usage profile, as section 1 says. Settings reasons go in the Decisions log at the retrospective too. The Deep and A/B go-ahead and the `/code-review` levels follow the usage profiles. Appendix G wires and describes the secrets guard and pins effort and compaction; the docs-writer and security-reviewer templates get `maxTurns`. The implementer tier names are explained against model tiers and sizes; the docs writer is noted as the one other agent that edits (documentation only). The DEV_CYCLE list in Phase 17 renders nested. The tagline, README and `AGENTS.md` describe Coldstarter as a spec for AI coding agents, with Claude Code as the reference platform. |
| 1.8.0 | 2026-09-29 | **Framework sizing, data views and grill mode.** Phase 2 ends by **sizing the framework**, None, Light, Standard or Full, from the project type, scale, risk and lifespan, and writes a short **dev plan**: the phases, agents, skills and gates the project gets, each with a reason, and a trigger for each thing left out. Sizing decides whether machinery exists; the usage profile still decides how it runs. Each size has its own **life cycle** and **upgrade trigger** (section 1); the handoff teaches only that life cycle (None and Light projects get no `/audit`, `/iterate` or `/autoiterate`), and the **definition of done** is marked by size. A new **`/devmanual`** skill prints a short developers' guide sized to the project (the level, its life cycle, the commands that apply, the current state and the upgrade trigger), and `/devmanual full` the whole `DEV_CYCLE.md`. **Data views and dashboards** (for example single-page HTML views for marketing teams) get an Appendix A row, intake questions (source, a refresh the audience can run, a metric dictionary, audience, sharing, sensitivity), invariants, a privacy rule, a chart-quality check and delivery rules. Phase 1 gains **grill mode**, one question at a time with a recommended answer, suggested for high-stakes or vague projects, drawing on Matt Pocock's `grill-me` skill. The security reviewer applies from Light up; a None deliverable gets a one-off security pass. |
| 1.7.0 | 2026-09-29 | **A design guide against generic defaults.** Anything with a user interface gets a **visual direction** in Phase 2 (who it's for, how it should feel, references, and what it must never look like), confirmed at the gate, and a **design guide**, `docs/DESIGN.md`, written with the design tokens before the first screen is built (Phase 4; template in the new **Appendix J**). The guide makes the tokens the only source of values, gives colour a job, sets type, density, states, motion and copy rules, and lists the statistically likely defaults that make an interface look machine-made (purple gradients, the three-card landing page, cards around everything, buzzword copy), each allowed only when the direction calls for it, with the reason written down. An organisation's own design system beats it. The UX reviewer and the implementers work from it, and the docs, the `CLAUDE.md` skeleton and the definition of done carry it. It's platform-neutral: it doesn't depend on any one tool's design skill. |
| 1.6.0 | 2026-09-29 | **Instructions as an index, routing chosen per project, and a platform-neutral rule.** Phase 6: the project instructions file is loaded and re-read on every turn, so it becomes an **index**: a size budget in bytes as well as lines, rules and numbers in it with their reasons in the Decisions log, a "Read when" list of the docs, and detail moved to skills, agents' own files, path-scoped instructions and docs; the usage report measures it. Phase 13: model and effort are no longer a fixed table. Once the project type, architecture and dev cycle are known, each role gets a **model tier** (strong, standard or light) and an effort level from how hard its work is, what a miss costs, how often it runs and the usage profile, with a reason, confirmed with the user; the old table becomes the reference build's routing, a starting point. Phase 10 lists starting tiers. A directive at the top and a new **Appendix I, Platform bindings**: the method is platform-neutral, and platform specifics belong in that appendix (Claude Code is the reference binding; its inline specifics move there in 2.0.0). Lesson 22 added. |
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

> **What this is.** A general launch pad for any serious project, for business or pleasure, solo or enterprise. You give it an idea, a business problem, a question to answer, a product or tool to build, or an integration to set up. It turns that into a working first version and a self-improving development framework, sized to the project: a one-off gets almost none of it, a small tool a light version, and a product that will keep changing all of it. The full framework includes:
>
> - version control (Git by default, or none for a one-off) with a CI/CD flow to match
> - readable, conventional code that a person can debug, commented to the level you choose
> - for anything with a user interface, a design guide that steers it away from generic, machine-made defaults
> - tests and quality gates
> - performance and correctness measurement
> - specialist reviewer agents
> - an evidence-based backlog with a triage and batching engine
> - `/audit`, `/iterate` and `/autoiterate` loops
> - deterministic hooks
> - token-efficient model routing
> - all the supporting docs
>
> **How to use it.** Open your AI coding agent (Claude Code is the reference platform) in an empty folder (or an existing codebase) and say:
>
> *"Read COLDSTARTER.md and launch a project for: <your idea, problem or question>."*
>
> Copy `COLDSTARTER.md` into the new folder first, or give the agent its full path.
>
> The agent interviews you, proposes a solution, a technology stack and a scale profile, builds v1, then sets up the framework at the size the project needs, stopping at key decision points. When it's done, a Standard or Full project runs `/iterate` for one development cycle, or `/autoiterate` to let it keep working through the backlog on its own.
>
> **Where it came from.** The method was distilled from building a real project end to end (Pocket Universe, a browser simulation) and its development loop, including the mistakes. Nothing here is game-specific. Every role and rule is stated generically, and Appendix A maps it to each kind of project.

---

## 0. Instructions to the agent

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
2. **Don't build what isn't needed.** If the best answer is an existing product, a spreadsheet, a no-code tool, a one-off analysis or a process change rather than a software project, say so in Phase 1, with your reasons. Only launch a project if the user still wants one, and size its framework (Phase 2): run only the phases in its dev plan, and add the rest when a trigger in the plan fires.
3. **Stop at the gates** marked 🚦. Between gates, make the reasonable call, say what you chose, and keep moving.
4. **Keep a progress file.** `docs/PROJECT_PROGRESS.md` holds the phase checklist and a **Decisions** log (date, decision, who made it, why). Its first line records where the framework came from: *"Launched with Coldstarter v<version> by Luke Bennie."* The project itself belongs to whoever the user names as its owner. A fresh session must be able to resume when the user says "continue the project launch". For None and Light, keep it to the dev plan and the Decisions log.
5. **Commit at the end of every phase** (`Phase N: …`), following the version-control flow of the chosen scale profile (with snapshots, take one instead; with no version control at all, there's nothing to commit; Appendix K). Never force-push or rewrite shared history. Ask before creating remote repositories, making anything public, deploying, or touching cloud accounts, billing or production.
6. **Check the current docs** before writing config: your AI coding platform's (subagents, skills, hooks, settings, tool servers; Appendix I links the reference platform's), and the chosen CI and hosting platforms'. The bindings and templates here reflect formats as of 2026-09. Verify them rather than assume.
7. **Adapt, don't transplant.** Every role has a domain equivalent (Appendix A). Rename and reshape it, but keep its *function*.
8. **Security by default, at every scale.** Never put secrets in the repo, in prompts or in agent context. Agents get the minimum tools they need. No agent gets production credentials. Respect any organisation policy or managed settings you find. Every project sized Light or above gets a dedicated **security reviewer** agent (Phase 10, Appendix C2), from the smallest hobby tool up, and a deliverable sized None gets a one-off security pass before it's delivered. Only the depth of the checks scales with the profile.
9. **Respect the token budget.** Ask about it in Phase 1 and set a **usage profile** (Lean, Balanced or Throughput; section 1) from the answer. It shapes the agent specs, the workflow and the code layout, not just the models. Measure where the usage actually goes (the usage report) rather than guess.
10. **Write docs for a person who starts cold.** Plain, direct sentences, tables for reference material, no filler.
11. **Write code a person can read and debug.** From the first line of v1: the language's standard conventions and idioms, clear names, the stack's usual file layout, tests that read as specifications, and comments at the level chosen in Phase 1 ("Readable code" in Phase 5). Readability gives way only where a measured need forces it, and the code says why.
12. **The product comes first. Process has to earn its place.** Every document, agent, skill, hook and gate costs time to make and to keep true, and on a small project that cost can outgrow the product. So create a piece of process only when the dev plan includes it, and include it only when it pays for itself on *this* project: it prevents a failure that's likely and costly here, or answers a question someone will actually ask. Keep the process in proportion to what the project is worth (the process budget, section 1). Leave out any template section that has nothing project-specific to say rather than filling it with boilerplate. The user can cut anything from the dev plan (the security minimum of ground rule 8 only after you've stated the risk in a line); record the cut and the trigger that would bring it back. When in doubt on a None or Light project, build the product and add the process later, when its trigger fires.

---

## 1. Scale profiles

Pick one in Phase 1. It decides how heavy every later phase is. Anything not marked for a profile is skipped for it.

| Dimension | **Solo** (personal, hobby, prototype) | **Team** (startup, small business, small team) | **Enterprise** (organisation, regulated, many stakeholders) |
|---|---|---|---|
| Version-control flow | Commit and publish to the main line. The local hook gates the publish. | Short-lived branches, a PR per batch, merged (squashed where the tool allows) after CI passes. | A protected main line, PRs with required reviews (code owners), signed commits if policy requires, release tags or branches |
| How `/iterate` ends | Publish to the main line and deploy | Open a PR. Merge after CI and a review. | Open a PR. A human approves and merges. The agent never self-merges. |
| Quality gates | Local hooks | Local hooks, plus the same commands in CI (CI is required) | CI is the authority. Adds security, dependency and licence scanning and required status checks. |
| Environments | Local and production (or just a deploy link) | Preview deployments per PR, then production | Dev, staging and production defined as infrastructure-as-code, with promotion and rollback |
| Secrets | An `.env` file on the ignore list, never committed | The hosting platform's secret store | Vault or KMS, with rotation and least-privilege access. Never in agent context. |
| Security review depth | A dedicated security reviewer on every audit and on sensitive changes: OWASP-style checklist, secrets and dependency hygiene | Plus a threat model kept current, security regression tests, and dependency and secret scanning in CI | Plus a formal threat model, SAST/DAST/SCA/IaC scanning as required checks, findings ready for a pen test, and a compliance mapping |
| Extra reviewers | None | A compliance reviewer if the domain is regulated | Compliance, infrastructure/SRE, accessibility, data governance, architecture |
| Docs | README, a user guide, ARCHITECTURE, a domain-logic reference, operations notes, DEV_CYCLE, a threat model, and a design guide for anything with a user interface (a Light project starts from README and adds the rest on their triggers; framework sizing below) | Plus decision records in `docs/adr/`, CONTRIBUTING and a changelog | Plus runbooks, SLOs, a data classification and onboarding docs |
| Operations | None, or console logging | Error tracking and basic metrics | Logging, metrics, tracing, SLOs, alerting, and incident and rollback runbooks |
| Compliance | None | Privacy basics (GDPR/CCPA if personal data) | Whatever applies: SOC 2, ISO 27001, GDPR, HIPAA, PCI DSS, accessibility law. Plus an audit trail. |
| Agent autonomy | High: implements, commits and publishes | Medium: implements and opens PRs; people merge | Low to medium: implements and opens PRs; people approve. No production access. Tools restricted. |
| Audit cadence | Rarely | Each milestone | Scheduled, with CI-driven checks in between |

Mixed cases are normal. For example, a solo developer building something that handles payments uses the Solo version-control flow but Enterprise-grade secrets handling and security review depth. Record every deviation in the Decisions log.

### Usage profiles

The scale profile sets how heavy the process is. The **usage profile** sets how much of the user's AI usage the project spends to get its work done, and it's chosen separately: a solo hobbyist on an entry-level plan and a funded team on an API budget can run the same scale profile under very different limits. Pick one in Phase 1.

Some savings cost nothing in quality, so every profile gets them: the usage report in every audit, a compaction window for the main session, a one-hour cache for agents that wait, turn caps, tests run once with the results passed to reviewers, one triage call per batch, and a named level for every change review (Phase 13). The profile only moves the real trade-offs:

| Dimension | **Lean** (usage-limited plan; cost first) | **Balanced** (the default) | **Throughput** (usage isn't a concern; speed and depth first) |
|---|---|---|---|
| Suggested for | An entry-level plan, or any plan where the user hits limits | A higher-tier or team plan, or an API budget with headroom | Enterprise or API use where time matters more than tokens |
| Agent models and effort | Phase 13 routing; observe-and-report roles (efficiency, code quality, user tester, accessibility) at low | Phase 13 routing | Phase 13 routing, with the strong tier also for the UX and product reviewers, and the Routine implementer used only for copy and styling |
| Change review level | `low` for `ui` and `tooling`; `medium` otherwise; `high` only when the user asks | `low` for `ui` and `tooling`; `medium` otherwise; `high` for Deep items | `medium` by default; `high` for Deep, security and data items |
| Deep items and A/B experiments | Ask before each one; prefer one well-argued approach over an A/B | Ask before A/B experiments | Run them as the backlog says |
| `/audit` | Core or focused audits, with borderline triggers skipped; `/audit full` only when the user asks | Core audits, with borderline triggers skipped; `/audit full` at major milestones | Core audits, with borderline triggers run; `/audit full` at each milestone |
| User tester in `/iterate` | Plays only the batch's changes, at the sizes they affect | Plays the batch's changes at desktop and phone sizes | Plays the batch's changes, plus a short newcomer pass every batch |
| Main session | Compacts at 200K | Compacts at 200K | Compacts at 400K, for more continuity |
| Batch caps | As Phase 11 (bigger batches mean fewer review cycles, but don't raise the caps: failures get harder to isolate) | As Phase 11 | As Phase 11 |
| Code layout (see "Readable code" in Phase 5) | A rule: files split by concern and kept small, tool output quiet by default, checked in code review | Strong guidance | Guidance |

The usage profile doesn't set how much the code is commented: that's the **comment level** (Phase 5), chosen by who will read and debug the code. A Lean project maintained by people can still be Human-maintained; it just pays for longer files knowingly.

Record the chosen profile in the Decisions log and in the project instructions file ("Model & effort"). Revisit it at each retrospective with the usage report's numbers: a Lean project whose reviews keep missing real bugs should move a dimension up, and a Balanced project whose user keeps hitting limits should move one down, one dimension at a time.

### Framework sizing

The scale profile sets how heavy each piece of process is, and the usage profile how much usage it spends. **Framework sizing** decides which pieces exist at all: a one-off script doesn't need a reviewer roster, and a marketing dashboard doesn't need a batching engine. Choose it at the end of Phase 2, once the project type, scale, risk and expected lifespan are known.

| | **None** | **Light** | **Standard** | **Full** |
|---|---|---|---|---|
| For | One-offs nobody will change later: a script, an analysis, a converted file, a single page | Small or short-lived tools, data views and dashboards, automations, internal utilities, prototypes | Products, services, integrations, libraries and games that will keep changing | Standard projects at Team or Enterprise scale, or that are regulated, or hosted with uptime targets |
| Phases | 1 (quick), 2 (short), 3's first step if it uses version control or snapshots, 4, and a short 17 (with the one-off security pass) | 1 (quick), 2 (short), 3, 4, then the **Light minimum set** below; everything else waits for its trigger | All, with only the agents and phases the project type needs | All, plus the scale profile's extra reviewers, with CI as the authority |
| Agents | None beyond the session | The security reviewer. The session implements; an implementer agent only on its trigger | The three always-on reviewers and the conditional ones the project needs (Phase 10), three implementers (one per work tier), triage | Standard's, plus the scale profile's extra reviewers |
| Skills | None | None by default. `/devmanual` and at most one small project skill (for example `/refresh` for a data view), each on its trigger | `/audit`, `/iterate`, `/autoiterate`, `/devmanual` | As Standard |
| Backlog | None | `docs/TODO.md`: a short list of known issues and ideas, no triage, kept in priority-class order (P1 to P5, Phase 11) | `BACKLOG.md` with triage and batches | As Standard |
| Version control | Optional: snapshots, or none at all | Recommended; snapshots or none need a reason in the Decisions log | Required | Required, with the scale profile's review flow |
| Before a change ships | Run it and look at it; a one-off security pass | The tests, the change review, and the security reviewer on sensitive changes | The full ship routine (Phase 6) | The full ship routine, gated by CI and people |
| Developers' guide | A "Working on this" section in README | A "Working on this" section in README; `docs/DEV_CYCLE.md` (about half a page) only when that outgrows the README | `docs/DEV_CYCLE.md` in full (Phase 17) | As Standard, plus the scale profile's docs |
| Life cycle | Deliver. A later change is a new request. | Ask for a change; it's implemented, tested and reviewed, then committed and deployed. Recurring chores, like a data refresh, run through the project skill if there is one. | `/audit` rarely; triage; `/iterate` or `/autoiterate` batch by batch | As Standard, through PRs and human approval |
| Upgrade when | Someone asks for a second round of changes, or it will be kept and used: go to Light | Change requests keep coming (more than a handful waiting), more than one person relies on it, or it becomes a shared tool: go to Standard, adding the backlog, triage and `/iterate` first, then the roster and `/audit` | It moves to Team or Enterprise scale, handles regulated data, or becomes a hosted service with uptime targets: go to Full | — |

Sizing is separate from the usage profile: sizing decides whether a piece of machinery exists, and the usage profile how the pieces that exist run. A size can go down as well as up: a finished product that only gets occasional fixes can drop from Standard to Light. Record either move in the Decisions log and the dev plan; an upgrade then runs the phases it adds.

**The process budget.** Process should cost a small fraction of what the project is worth (ground rule 12). A $100 internal utility gets minutes of process, not a day of it. When you write the dev plan (Phase 2), estimate the framework work that follows v1 (the phases after Phase 4, apart from building the product) and compare it with the estimate for v1 itself. For None and Light, the framework work must come in well under v1's; if it doesn't, cut items, starting with the ones least likely to catch a real problem on this project, until it does. The security minimum (ground rule 8) is never cut to fit the budget. For Standard and Full, say in the dev plan what the framework costs and what it buys. The user can set the budget directly, for example: *"We're building a $100 internal utility. Don't write architecture documentation unless the complexity warrants it."* That instruction is followed as given, and logged.

**The Light minimum set.** A Light project always gets these, each kept as short as it can be:

- the dev plan and the Decisions log (ground rule 4)
- a README: what it is, how to run it, how to use it (the user guide), and "Working on this": the life cycle, the commands, and the upgrade trigger (the developers' guide)
- readable code at the chosen comment level (Phase 5, "Readable code")
- a short project instructions file (Phase 6): the files, the ship routine, the conventions, a few lines on security, and only the targets and invariants the project actually has
- one test command with a quick mode, covering the core logic and a run of the whole thing end to end (Phase 7)
- the security reviewer on sensitive changes, and the change review
- the after-edit check, the secrets guard and a tests-only publish gate (Phase 14)
- `docs/TODO.md` for known issues and ideas
- one review of v1 and a short retrospective (Phase 16)

Everything else is added only when its trigger fires. Write the triggers that apply into the dev plan, so the omissions are decisions rather than gaps:

| Piece | Add it when | Until then |
|---|---|---|
| `docs/ARCHITECTURE.md` | The code grows past a handful of files or components, or someone other than the owner and the agent will work on it | A "How it's built" paragraph in README |
| The domain-logic reference | The rules aren't obvious from the code and tests (money, dates, eligibility, metric definitions). A data view always keeps its metric dictionary (Appendix A). | The tests, named as specifications |
| `docs/OPERATIONS.md` | Deploying or rolling back takes more than one command, or someone else deploys it | A line in README |
| `docs/THREAT_MODEL.md` | It handles personal, financial or health data, has logins, or takes input from people outside the team | The security lines in the project instructions file |
| `docs/DESIGN.md` | The interface grows past a couple of screens, or people outside the team use it | The design tokens, with the visual direction in a few lines at the top of the tokens file |
| `docs/USER_GUIDE.md`, `docs/DEV_CYCLE.md` | Their README sections outgrow a screen or two | The README sections |
| Invariant, accessibility and further security tests (Phase 7) | The domain has invariants (for example totals that must reconcile), people outside the team use the interface, or there's a login or an input boundary to test | The core-logic and end-to-end tests |
| Benchmarks (Phase 8) | Speed is a target | — |
| Environment helper, browser tool (Phase 9) | Running the product takes more than one command, or checking it needs a browser | The run command in README |
| An implementer agent (Phase 10) | The work needs a different model tier from the session's | The session implements |
| `/devmanual` (Phase 12) | More than one person works on it, or the life cycle has more than a couple of commands | README's "Working on this" |
| A project skill (Phase 12) | A chore recurs, such as a data refresh | A command in README |
| Model routing (Phase 13) | An implementer agent is added | The session's and the security reviewer's tier and effort, in the dev plan, confirmed at the Phase 2 gate |

The same test applies at every size: the Standard and Full phases say what a project *may* need, and the dev plan keeps only what this one does.

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
3. Ask the follow-up questions, in one of two modes. The user can switch at any time.
   - **Quick round** (the default): one grouped round of at most about 15 questions, each with a suggested default, so the user can answer "defaults are fine" to most of them.
   - **Grill mode:** one question at a time, each with a recommended answer and a line on why. Settle the decisions others depend on first (the problem and who it's for, then scale, data and security, then form and stack). Don't ask what the existing code or documents can answer: check them and say what you found. Stop when the problem analysis and the dev plan have what they need. Log every answer in the Decisions log. Suggest grill mode, with a one-line reason, when the project is at Team or Enterprise scale; handles money, health or personal data; integrates with external systems; makes irreversible changes (data migrations, deletions, anything public or financial); is meant to last; or when the requirements stay vague after the first answers. It draws on Matt Pocock's `grill-me` skill ([mattpocock/skills](https://github.com/mattpocock/skills)), which interviews the user about a plan until every branch of the decision tree is resolved.

   Cover these topics in either mode:
   - **Problem and value:** what problem, for whom, what they do today, and why now. For business projects, add the stakeholders, the decision-maker, the business case and the KPIs the project should move.
   - **Success:** what "working" looks like, with measurable targets (speed, accuracy, cost, conversion, time saved, uptime).
   - **Form and platforms:** web, mobile, desktop, CLI, API, pipeline, report or dashboard, AI assistant. Desktop, phone or both. Online, offline or both.
   - **Scale:** expected users, data volumes and load, now and in a year. Team size, and who reviews and approves work.
   - **Constraints:** budget (build and running cost), what the project is worth to the user (for example "a $100 internal utility"; it sets the process budget, section 1), deadlines, hosting or cloud preferences, and existing systems to integrate with.
   - **Security and compliance:** personal, financial or health data; authentication and SSO needs; regulatory regimes; data residency; audit needs.
   - **Stack:** languages, frameworks and clouds the team knows or mandates, or "no preference".
   - **Data:** sources, ownership, sensitivity, volume, and whether it's fresh or historical. For a data view or dashboard, also: where the data comes from and who can access it; how it's refreshed, as a step its audience can run themselves; the metric definitions (a metric dictionary); who reads it; how it's shared and forwarded; and how sensitive it is (Appendix A).
   - **Version control:** Git (the default), another tool the organisation uses (such as Subversion, Mercurial or Perforce), **snapshots** (dated copies of the project folder, no tool needed), or **none** (no history kept at all). Check a tool is installed, and if not, give the install command for the user's system and ask before installing it (Appendix K). Suggest Git unless there's a reason not to; snapshots and none suit only one-offs and small tools (the framework sizes in section 1), and with none, say in a line that a change can't be undone once the session ends.
   - **Distribution:** public or private repository, open source or proprietary, where the repository is hosted (GitHub, GitLab, an organisation server, or only on this machine), and where it deploys.
   - **Identity:** project name, author or organisation for commits and copyright headers, and licence.
   - **Working style:** how autonomous the agent should be, and how often the user wants to review.
   - **Usage plan and ethos:** the AI platform's plan or API budget, whether the user hits usage limits, and how much the project should optimise for usage in its agent specs, workflow and coding style: cost first (Lean), balanced, or speed and depth first (Throughput). Suggest the profile from the plan (an entry-level plan → Lean, a higher-tier or team plan → Balanced; Appendix I maps the reference platform's plans), and say in a line what each would change.
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
   - the **recommended scale profile**, **usage profile**, **comment level** and **project type**, with reasons, and a first guess at the **framework size** (Phase 2 settles it)

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
5. For anything with a user interface, propose a **visual direction**, so the design comes from this product rather than from the most likely defaults:
   - who it's for and where they'll use it (device, setting, how much time they have)
   - three words for how it should feel (for example *calm, exact, warm*)
   - two or three references, and what to take from each
   - what it must never look like (for example "a generic SaaS landing page")
   - how dense it should be: a tool or dashboard is compact and scannable, a reading or marketing page is generous

   If the organisation has a design system or brand guidelines, the direction starts from them. The direction becomes the top of `docs/DESIGN.md` (Phase 4, Appendix J), or, in a Light project without a guide yet, the top of the tokens file.
6. Propose a **v1 feature list** that can be built in one or two sessions, plus an architecture sketch (components, data flow, hot paths, trust boundaries).
7. **Team and Enterprise:** write the key choices as decision records (`docs/adr/0001-<title>.md`: context, decision, alternatives, consequences).
8. **Size the framework** (section 1, "Framework sizing"). From the project type, the scale, the risk (money, health or personal data, irreversible changes, external integrations) and the expected lifespan, recommend None, Light, Standard or Full. Write a short **dev plan** in `docs/PROJECT_PROGRESS.md`: the phases, agents, skills and gates the project gets, each with a reason, and for each thing left out, the trigger that would add it (for example "add benchmarks when a page takes over a second to load"). Start a Light plan from the Light minimum set, and any plan from the smallest set that fits, rather than cutting down from everything. Check it against the process budget (section 1), and apply any limit the user has set. For Light, the plan also names the session's and the security reviewer's model tier and effort, so Phase 13 needs no gate of its own. Check the version control chosen in Phase 1 fits the size (section 1), and raise it with the user if it doesn't. From here on, run only the phases in the dev plan, and keep the phase checklist to those.
9. 🚦 **Gate:** the user confirms the stack, the version control, the non-functional requirements, the pillars, the visual direction (if there's a user interface), the v1 scope, and the framework size with its dev plan and its cost against the process budget.

---

## Phase 3: Repository, version control and delivery pipeline 🚦 (before creating any remote or cloud resource)

1. Set up the version control chosen in Phase 1 (Appendix K): check it's installed, create the repository, set the author identity **for this repository only**, and name the main line (`main` in Git). With snapshots, set up the snapshot folder instead, outside the project folder. With none, skip this step.
2. Add:
   - with a version-control tool, the ignore list (`.gitignore` in Git; Appendix K) for the stack, plus build outputs, test and bench outputs, caches, `.env` and secrets
   - `README.md`
   - a licence or a proprietary notice
   - `docs/PROJECT_PROGRESS.md`
   - Team and Enterprise: `CONTRIBUTING.md`, and a PR template
   - Enterprise: code owners (`CODEOWNERS` on Git hosting), `SECURITY.md`, and a changelog convention
3. Set the **header convention** for new source files (the copyright or licence line, then a line or two saying what the file holds) and the **code conventions** ("Readable code" in Phase 5): the language's standard style guide or the organisation's own, a formatter and linter where the stack allows them (for example Prettier and ESLint, Black or Ruff, gofmt, rustfmt), the test framework's usual layout, and the comment level from Phase 1. Record them in the project instructions file.
4. 🚦 **Ask before creating the remote** (the shared repository, if there is one): the host, the account or organisation, public or private (for example `gh repo create` for GitHub). Then:
   - **Solo:** publish the main line (if there's a remote) and set up deployment (static hosting, a platform deploy).
   - **Team:** add CI, running the same test and bench commands the local hooks run, plus preview deployments per PR, and protect the main line so merging requires CI to pass.
   - **Enterprise:**
     - CI with required status checks, plus security, dependency (SCA) and licence scanning
     - main-line protection with required reviews
     - environments (dev, staging, production) as infrastructure-as-code, with promotion and rollback
     - secrets in the approved store
     - deployment credentials that agents never see
5. Commit: `Phase 3: repository and delivery setup`.

---

## Phase 4: First working version (MVP) 🚦

**Goal:** something real that runs end to end, not scaffolding.

1. Build the v1 features in the chosen stack. Keep the structure as simple as the stack allows, and write to the code conventions from the first line ("Readable code" in Phase 5): clear code costs no more to write than unclear code, and far less than cleaning it up later.

   For anything with a user interface sized Light or above, set up the **design tokens** (colour, type scale, spacing, radii, shadows, motion) *before* building the first screen, then build every screen from them. Standard and Full also write `docs/DESIGN.md` from the template in Appendix J before that first screen. Light puts the visual direction in a few lines at the top of the tokens file, and writes the guide when its trigger fires (section 1). A None deliverable follows the visual direction without tokens or a guide.
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

**Light:** steps 2 and 6 always. Steps 1, 3, 4 and 5 wait for their triggers (section 1, the Light minimum set); until then, a paragraph in README or a few lines in the project instructions file does their job.

1. Write `docs/ARCHITECTURE.md`:
   - a table of the files or components
   - the code layout
   - **how one unit of work flows** (a frame, a request, a job, a pipeline run), as a small diagram
   - the **hot paths**, and where cost grows with scale
   - trust boundaries and data flows (Team and Enterprise)
   - determinism notes
   - test, bench, build and deploy commands
2. Fill any gaps in testability left over from Phase 4.
3. Write the **domain-logic reference** (`docs/<DOMAIN>.md`, for example `SIMULATION.md`, `BUSINESS_RULES.md`, `PIPELINE.md`, or `METRICS.md` for a data view): the rules the system follows and why, with their constants, edge cases and invariants, citing functions rather than line numbers. The domain-correctness reviewer checks it. ARCHITECTURE says where code lives; this says what it does.
4. Write **operations notes** in `docs/OPERATIONS.md`: how to build, deploy, verify what's live (a version or build stamp), and roll back. For Solo, a few lines. Team and Enterprise extend it into runbooks (Phase 15).
5. Write a **threat model** in `docs/THREAT_MODEL.md`: assets, actors, trust boundaries, entry points, top threats, mitigations. For Solo, half a page is enough, since even a static site has third-party scripts, user input and a deploy pipeline. For Team and Enterprise, add a **data classification** for every data store. The security reviewer keeps it current.
6. Set up **readable code**, for the people who debug it and for agents. An agent writes clear, conventional code as fast as unclear code, and it pays back in every debugging session, human or agent. And every agent turn re-reads what the agent has read so far, so the size of what it must read to make a change is a running cost.
   - **Always** (every comment level; the layout items' strictness follows the usage profile, below; checked in code review and by the code-quality reviewer):
     - **Conventions:** follow the language's standard style guide and idioms (for example PEP 8, Effective Go, the Rust API guidelines, a well-known JavaScript or TypeScript guide) or the organisation's own, with a formatter and linter enforcing them where the stack allows. Prefer the well-known way to do a thing over a clever one.
     - **Names** say what things are: full words in the language's naming convention, units where they matter (`timeoutMs`, `massKg`), booleans that read as questions (`isVisible`, `hasErrors`), no single letters outside short loops and standard maths, and one name per concept across the codebase. Keep them distinctive and searchable, and cite code by file and function name, so people and agents find things with one search instead of paging.
     - **Structure:** the stack's conventional project layout; code split by concern into files that can be read whole (a few hundred lines, not thousands); functions that do one thing; and `docs/ARCHITECTURE.md` saying what lives where, so a reader opens only the files a change touches.
     - **File headers:** every source file starts with the header convention (Phase 3), including a line or two on what the file holds.
     - **Tests read as specifications:** the framework's usual layout, mirroring the source; names that state the behaviour (`test_merge_conserves_momentum`); arrange, act, assert; one behaviour per test; no logic beyond setup; and failure messages that show the expected and actual values.
     - **Debuggable errors:** fail loudly, with a message naming what failed and the values involved. Never swallow an error silently.
     - **Quiet tools:** tests, builds and benchmarks print a one-line summary on success and the details only on failure (with a `--verbose` flag for more).
     - **Generated output** (bundles, minified or compiled files) is marked as generated, and points readers at the source, which stays readable.
   - **Comment level**, chosen in Phase 1 and recorded in the project instructions file (Conventions):

     | Level | For | Comments |
     |---|---|---|
     | **Agents-first** | Code only agents maintain, where usage matters most | File headers, and comments only where the reason isn't obvious from the code (a workaround, a constraint, where a tolerance comes from) |
     | **Standard** (the default) | Most projects: a person reads the code now and then, to debug or review it | Plus a doc comment on every public function, class and module, in the language's convention (JSDoc, docstrings, Javadoc, rustdoc, XML doc comments): purpose, parameters with units, return value, errors |
     | **Human-maintained** | Handoffs, teams, learning projects, long-lived code that people will own | Plus doc comments on internal functions, a comment on every non-obvious block (algorithms, maths, state machines, concurrency), and a short "how to debug this" note in each module's header: what to inspect or log, and the known failure modes |

     At every level, comments explain *why* and name the constraints; they don't restate *what* the next line does, because a comment that repeats the code goes stale and misleads. A change that makes a comment wrong updates it. Higher levels make files longer, and every agent that reads a file pays for its length on each turn, so choose the lowest level that serves the people who will read the code.
   - **Where readability gives way:** a measured hot path may trade clarity for speed, with a comment giving the reason and the measurement; shipped output may be minified or bundled while the source stays readable. Nothing else is an exception.
   - The Structure and Quiet tools items above are rules, checked in code review, under a Lean usage profile; strong guidance under Balanced; and guidance under Throughput (section 1). The rest of the list always applies.
7. Commit.

---

## Phase 6: The project instructions file, the project's constitution 🚦 (for the targets)

The **project instructions file** (Appendix I names it for each platform) is loaded at the start of every session and re-read on every turn, by the main session and by every agent told to read it. So it holds what every session needs, and it's an **index** to everything else (skeleton in Appendix B):

- **The project in brief:** what it is, a table of the important files, the scale profile.
- **Before every change ships:** the numbered routine, sized to the dev plan (Light: the tests, the change review, and the security reviewer on sensitive changes). Standard and Full: run the tests, then the benchmark comparison, then the change review, then the user-tester agent, then the domain-correctness reviewer if the core logic changed, then the **security reviewer** if a sensitive area changed (authentication, input handling, secrets, dependencies, headers/CSP, CI/CD, infrastructure, data access, LLM prompts or tools). Then publish, or open a PR, and republish or deploy.
- **Targets:**
  - **Performance budgets** with numbers (for example "p95 API latency ≤ 200 ms at 50 requests/s", "60 fps for the everyday scenario", "a nightly job finishes in ≤ 15 minutes", "bundle ≤ 200 KB gzipped")
  - a **stretch target**, marked as a backlog goal
  - **no steady memory growth**
  - **reliability**, for Team and Enterprise (availability or error-rate SLOs)
  - **cost** (a monthly cloud budget)
- **Correctness:** the domain's **invariants** with tolerances (Appendix A).
- **Security and compliance:** the controls and regimes that apply, and what counts as a sensitive change.
- **Design pillars.**
- **Workflow:** audit, iterate, batches, bench, A/B experiments, the version control and its flow for this profile, unattended runs (off unless the user opts in). Light: the change-request life cycle and the project skill (section 1).
- **Model and effort:** the routing table from Phase 13. The reasons and dates go in the Decisions log.
- **Conventions:** commit authorship, header, the code conventions (style guide, formatter and linter, naming, test layout) and the comment level, platform and input conventions, "keep README in step", "all randomness through `rand()`".
- **Read when:** an index of the docs, one line each, saying when to read which (for example "changing the core logic → `docs/<DOMAIN>.md`", "UI work → `docs/DESIGN.md`", "shipping → `docs/DEV_CYCLE.md`", "why a setting is what it is → the Decisions log").

**Keep it an index.** Every line is paid for on every turn of every session, so:

- Keep it within a **size budget** of about 150 lines *and* 10 KB, whichever comes first. Measure bytes as well as lines, because long lines hide size (Appendix H, lesson 22).
- State rules and numbers, not their history. The reasons behind a setting and its past values go in the Decisions log (`docs/PROJECT_PROGRESS.md`).
- Put detail where it's loaded only when it's needed: procedures in skills, a role's instructions in that agent's own file, rules for one area of the code in **path-scoped instructions** (loaded only when files in that area are read), and reference material in `docs/`. Imports that pull another file in at session start don't save anything.
- Start every doc with a line saying what it covers, so a reader can tell from the index whether to open it.
- Brief agents with file paths and the excerpts they need, not whole docs.
- When a doc is added, renamed or split, update the index in the same change.

**Light:** aim for well under the budget, about 50 lines. Give the targets and invariants the project actually has, and don't invent budgets for a tool that has none. Leave out any section of the skeleton with nothing project-specific in it.

🚦 **Gate:** propose the numeric targets, the invariants and the security controls, and get them confirmed. Light: only if there are targets or invariants to confirm; the security lines were agreed at the Phase 2 gate. Then commit.

---

## Phase 7: Tests and invariants

**Light:** step 1 (`--screens` only if there's a user interface), step 2 for the core logic and one end-to-end run, step 5's secret scan and dependency audit, and step 6. Invariant, accessibility and further security tests wait for their triggers (section 1).

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
   - When `--compare` fails, set the change aside and re-measure the untouched code, then restore the change (a set-aside A/B test; `git stash` and `git stash pop` in Git, Appendix K) before blaming the change.
   - **Re-baseline only after a genuine improvement.** If the machine got slower, ask the user, and commit the re-baseline on its own with a clear reason.
6. Commit the first baseline, and set the budgets in the project instructions file from real numbers.

---

## Phase 9: Tool access for agents

1. A **local environment helper** (for example `tools/serve.py`, `make dev`, a Docker Compose file) that starts the app and its dependencies if they aren't running, and waits until they're ready.
2. For UIs, configure a **browser tool** (for example the Playwright or Chrome DevTools MCP server; Appendix I says where it's configured) so agents can use the product, take screenshots and read the console. For services, give agents the test endpoint and seeded test data, **never production**.
3. **Plan for tools failing.** Every agent that uses the browser tool falls back to the scripted test runner and its screenshots, and says so in its report.
4. If there's a hosted copy or demo build, add an idempotent build script (it skips the rebuild when the source hash hasn't changed) that strips anything used only in development.
5. **Enterprise:** list which tool servers and tools are allowed, and restrict agent tools accordingly (each agent's tool list, plus the permission rules in the project settings; Appendix I).
6. Commit.

---

## Phase 10: Agent roster

Create the agents as subagents (templates in Appendix C; Appendix I says where each platform keeps them and how to write their settings). A **Light** project gets only the security reviewer: the session implements, and an implementer agent (on the standard model tier by default) is added only when its trigger fires (section 1). A **None** project gets no agents. The rest of this phase is for Standard and Full.

### Reviewers and auditors (read-only: never give them edit tools)

Each reviewer reads the project instructions file first. **Every finding needs evidence**: a metric, a screenshot path or a `file:line`. Triage discards findings without evidence.

Reviewers are the framework's biggest source of fan-out: every one dispatched re-reads the code and its brief, so nine reviewers on every audit add up to a large amount of usage, however cheap each is on its own (Appendix H, lesson 19). So only three reviewers run on every audit. The rest are **conditional**: each is created only if the project needs the role, and dispatched only when its trigger fires (`/audit`, Phase 12). Each role's model tier (strong, standard or light) and effort are a starting point; Phase 13 sets them for the project.

**Always on** (every Standard and Full project, and every `/audit`):

| Role | Job | Starting model tier / effort |
|---|---|---|
| **domain-correctness reviewer** | Reviews changes to the core logic for correctness, edge cases, stability and the invariants. Measures rather than guesses. | Strong / medium |
| **code-quality reviewer** | The whole codebase, not a diff: coupling, duplication, error handling, gaps in test coverage, and readability (the code conventions, names, file headers, test structure, comments at the chosen level, stale comments). Respects the stack decision. | Standard / medium |
| **security reviewer** | A dedicated security specialist, on **every project sized Light or above, and every audit**, and on any change that touches a sensitive area. Covers the threat model, authentication and authorisation, input handling and injection, secrets, dependencies and supply chain, headers and CSP, CI/CD and infrastructure config, data protection, and LLM-specific risks. Full spec in Appendix C2. | Strong / medium |

**Conditional.** "Changed" means changed since the last audit, as listed by `/audit` step 2. The full (milestone) audit and a focused audit that covers the role run it regardless of its trigger.

| Role | Job | Created when | Runs in `/audit` when | Starting model tier / effort |
|---|---|---|---|---|
| **perf profiler** | Runs the benchmarks, reads the hot paths, proposes measured improvements tied to a budget. | Speed or resource use is a target (the project has benchmarks, Phase 8) | A budget is missed or `bench --compare` regresses, or code on a hot path changed | Strong / medium |
| **UX reviewer** | Screenshots and live use at several viewport sizes: discoverability, feedback, hierarchy, touch, keyboard, contrast, reduced motion, accessibility. Checks the work against `docs/DESIGN.md`: values come from the tokens, every state is designed, and the generic defaults it lists are avoided unless the direction calls for them. | There's a user interface | UI code, styles, copy or the design tokens changed | Standard / medium |
| **user tester** | Runs the tests with screenshots, then uses the product live as **three personas**: *newcomer* (the first 60 seconds, arriving cold), *power user* (builds something deliberate), *breaker* (spams input, extreme values, resizing, switching mid-action). Gives a ship verdict or audit findings. In `/iterate` it plays the batch's changes, starting from the test run it's given; the full three-persona sweep is for audits. | Users drive the product (a UI, a CLI, an API others call) | Behaviour a user sees changed. When the UX reviewer also runs, its personas fold into the UX reviewer's pass instead. | Standard / low |
| **accessibility reviewer** | WCAG conformance with evidence. | A user interface with an accessibility target the UX reviewer can't cover alone (Enterprise, or wherever the law requires it) | UI code or accessibility checks changed, or the accessibility checks fail | Standard / low |
| **docs writer** | Writes the user guide, the domain-logic reference and the operations notes, and in an audit checks the docs the drift check flags against the code and product, including features that shipped undocumented. The one non-implementer allowed to edit, and only docs. Domain docs need their expert's review. | Every Standard and Full project (it writes the docs in Phase 17) | The drift check (`/audit` step 2) flags a doc | Standard / medium |
| **efficiency auditor** | Bytes, dependencies, network, dead code, memory growth, running cost, what ships in each artefact. | Every Standard and Full project | Only in the full (milestone) audit or `/audit efficiency`, or when a size or running-cost budget is missed | Standard / low |
| **product / experience designer** | Is it valuable and pleasant? First-minute experience, "aha" moments, missing capabilities, sharing. Small shippable ideas that serve the pillars. For business tools, "time to answer" and workflow fit. | Products and user-facing tools | Only in the full (milestone) audit or `/audit product`, or when the user asks for ideas | Standard / medium |
| **infra / SRE reviewer** | Infrastructure-as-code, deployment safety, rollback, observability, SLOs, cost. | Hosted services (Team and Enterprise) | Infrastructure, CI/CD or deployment config changed | Standard / medium |
| **data reviewer** | Schema, lineage, data quality, idempotency, reproducibility. | Pipelines, analytics, migrations | Schemas, pipelines, migrations or data fixes changed | Standard or strong / medium |
| **compliance reviewer** | Checks the change against the applicable regime's controls, audit trail and data handling. | Regulated domains | Code handling regulated data, or the controls themselves, changed | Standard / medium |
| **evaluation reviewer** | Eval results, prompt regressions, grounding, refusals, cost. | AI/LLM apps | Prompts, models, tools or the eval set changed, or the eval pass rate dropped | Strong / medium |

**Finding format** (every auditor uses it):

```
### [AREA-###] Short title
- **Area:** perf | core | ux | design | efficiency | code | security | infra | data | docs | usage | <domain>
- **Evidence:** <metric / screenshot path / file:line>
- **Impact:** 1–5   **Dev effort:** 1–5   **Class:** P1–P5 (proposed; triage decides, Phase 11)
- **Proposal:** what to change, why, and the expected gain
```

Give each agent a **"Known quirks (not bugs)"** list that grows over time. Quirks of the test environment shouldn't come back as findings every audit.

### Implementers (the only agents that edit code)

There are three implementers with the same instructions, one per **work tier** of the backlog. They implement, test and report per item. They **never commit, publish, merge or deploy**.

| Agent | Starting model tier / effort | Takes |
|---|---|---|
| `implementer` | Standard / medium | **Routine** work: effort-1 items outside the core logic (ux, design, efficiency, code, docs) |
| `implementer-hard` | Strong / medium | **Hard** work: core-logic, perf or security items, changes to the logic of the safety gates (hooks, the build, the test runner's pass/fail logic), or anything at effort 2+ |
| `implementer-deep` | Strong / high | **Deep** work: the project's hardest class of work (for example concurrency, consistency or transactions, numerical cores, security-critical cryptography or authentication, data migrations, major architecture changes) and A/B experiments |

Work tiers (Routine, Hard, Deep) label backlog work; model tiers (strong, standard, light) label models; framework sizes (None, Light, Standard, Full) label projects. Phase 13 can put any work tier on any model tier, and in a Light-sized project the session (or its one implementer, if it has one) takes every item.

Every implementer report includes:

- what changed per item, with `file:line`
- the tests added, and the test result
- **any behaviour change beyond the item's scope.** In the reference project, the first iteration added per-scene defaults and silently made "restart" discard the user's own settings everywhere. The code review caught it.
- **before and after measurements** for any performance claim. A claimed 64 → 37 ms speed-up didn't reproduce, and the Done entry had to say "benefit unverified".

### Triage: the backlog and batching engine

`triage` turns findings into `BACKLOG.md` and groups the work into batches (Phase 11). Start it on the standard tier at **medium** effort. At low effort, its first attempt at grouping broke its own rules: it mixed tiers, put effort-3 items outside `solo`, and miscategorised a refactor of core-logic identifiers. The full rules are in Appendix D.

Commit the roster.

---

## Phase 11: Backlog, triage and batching

1. Create `BACKLOG.md` (template in Appendix E) with these sections: **Batches, Ready, In progress, Rejected**, and `BACKLOG_DONE.md` for **Done**. Shipped items live in their own file because everything that reads the backlog (the orchestrating session, triage) would otherwise pay for the whole history on every read.
   - **Ready** columns: ID, Area, Title, Impact, Effort, Priority, Batch, Tier, Est. time, Evidence.
   - **Done** columns: ID, Title, Tier, Actual time, Result (metric delta or notes), Commit or PR.
2. **Priority is lexicographic: class first, then score.** A plain impact ÷ effort ratio lets a cosmetic fix (impact 2, effort 1: 2.0) outrank a security hole (impact 5, effort 3: 1.67). So every item gets a **priority class**, and impact ÷ effort only orders items *within* a class:

   | Class | Holds | Evidence it needs |
   |---|---|---|
   | **P1** Safety and security blockers | Critical or high security findings, exposed secrets, a safety gate (a hook, the publish gate, the test runner's pass/fail logic) that lets bad changes through, anything that could harm users or others | An exploit path, a scan result or a failing check |
   | **P2** Correctness and data-loss blockers | Wrong results, lost or corrupted data, a broken invariant, crashes or hangs in normal use | A failing test or invariant, or a reproduction |
   | **P3** User-visible regressions | Something that worked and no longer does, or got measurably worse: a finding that matches a Done item, a benchmark regression, a budget that was met and now isn't | The before and after, or the Done item it matches |
   | **P4** High-impact improvements | Improvements at impact 4 or 5, and any medium or low security finding or conformance gap (accessibility, compliance), whatever its impact | As any finding |
   | **P5** Efficiency and polish | Everything else | As any finding |

   The class comes from the *kind* of problem and its evidence, not from the impact score, so inflating an impact can't move an item past a class. A finding claimed for P1 to P3 without that class's evidence drops to P4 or P5. Only the user can move an item across classes (a pin), and triage says so in its report. The Priority column shows both, class first (for example `P1 1.67`).
3. **Triage rules:**
   - discard findings without evidence, and merge duplicates
   - priority: class, then impact ÷ effort within the class, then ties broken by risky behaviour first, then smaller changes
   - a finding that breaks a pillar goes to Rejected
   - never reorder In progress, Done or Rejected
   - a finding that matches a Done item (search `BACKLOG_DONE.md`) is a regression
   - IDs follow the pattern `AREA-###`
   - every Ready row gets a Tier and an Est. time
   - regroup every Ready item into batches on every run, and self-check the result
4. **Batching.** Each batch has **one category and one work tier**. Its reviews run once, for the whole batch. Batches are numbered in the order of their highest-priority item, so B1 always holds the top Ready item; lower-class items can ride along in a batch of the same category and tier, but never move a batch ahead.

| Category | Contents | Reviews | Max size |
|---|---|---|---|
| `ui` | Presentation, layout, copy, UI affordances. No change to the core logic. | change review + user tester | 15 (about 5 when they're effort-2 features) |
| `tooling` | Only tests, benchmarks, tools and docs. The product doesn't change. | change review only. No user testing or redeploy. | 15 |
| `core` | The domain logic: business rules, calculations, simulation, workflows | change review + user tester + one domain-correctness review | 5 |
| `perf` | Speed and efficiency work that isn't Deep work | change review + user tester + benchmark focus (+ correctness review if core is touched) | 5 |
| `security` | Authentication, authorisation, input handling, secrets, dependency upgrades with CVEs | change review + security reviewer (+ user tester if the product's behaviour changes) | 5 |
| `infra` | CI/CD, infrastructure-as-code, deployment and configuration | change review + infra/SRE reviewer, then a staging deploy (Enterprise) | 5 |
| `data` | Schema migrations, pipelines, data fixes | change review + data reviewer, run against a copy first. Migrations are usually `solo`. | 3 |
| `solo` | Deep work, A/B experiments, effort 3, irreversible changes, or anything that would conflict | That item's own full pipeline | 1 |

   The size caps limit the damage: bigger batches make it harder to tell which change broke something, spread reviewers' attention thinner, and make pulling out one bad item riskier. Core, security, infra and data changes interact. Polish rarely does. In the reference project, 14 small `ui` and `tooling` fixes shipped in about 35 minutes, against an estimated 175–245 minutes done one at a time.
5. **Estimates** (planning figures, not measurements):
   - Routine, effort 1: 10–20 min
   - Hard, effort 1: 15–25 min
   - effort 2: 20–35 min
   - effort 3 or Deep: 35–90+ min
   - a batch: its largest item's estimate, plus 2–3 min per extra `ui` or `tooling` item and about 5 min per extra item in the other categories

   Record the **actual time** in Done, and recalibrate from real runs.
6. **Check bulk edits mechanically.** After any bulk change to the backlog, run a short script: every Ready item is in exactly one batch, work tiers match, effort-3 items are `solo`, no batch is over its cap, and Ready is sorted by class, then score. In the reference project, a single hand edit silently dropped 12 rows.
7. Commit.

---

## Phase 12: Skills: `/audit`, `/iterate`, `/autoiterate` and `/devmanual`

Standard and Full: create the skills `audit`, `iterate`, `autoiterate` and `devmanual` (skeletons in Appendix F; Appendix I says where each platform keeps skills and how they're invoked). Light: none by default; `/devmanual` and at most one small skill for a chore the project repeats (for example `/refresh`, which rebuilds a data view from a new export), each when its trigger fires (section 1); never `/audit`, `/iterate` or `/autoiterate`. None: no skills. Don't pin an effort level in any skill, so the session's effort still applies.

**`/audit`**: rare and expensive; the backlog's source of truth. It comes in four kinds:

| Command | Runs |
|---|---|
| `/audit` | The **core audit**: the three always-on reviewers, plus each conditional reviewer whose trigger fires (Phase 10) |
| `/audit full` | The **milestone audit**: every reviewer the project has. For milestones (a release, the end of a stage of the roadmap), as often as the usage profile allows (section 1) |
| `/audit <focus>` | Only the agents that cover the focus (for example `/audit perf`, `/audit security`, `/audit efficiency`) |
| `/audit usage` | Only the usage report and triage, with no reviewer |

1. Check the build is current and the tools work.
2. Collect evidence, mechanically, before any agent runs:
   - the tests with `--screens` (only if a UI reviewer will run), `bench --compare`, the security scans (Team and Enterprise), and the usage report (`tools/usage_report.py --since last --save`, Phase 13), whose findings go straight to triage with no agent reading them
   - **what changed:** the files changed since the commit recorded on `BACKLOG.md`'s "Last audit" line, grouped by the conditional reviewers' triggers (core logic, hot paths, UI and styles, infrastructure and CI, schemas and pipelines, prompts and evals, docs). The first audit counts the whole project as changed.
   - **the docs drift check,** a script or a few searches, no agent: a doc whose code changed since the doc did, or a doc that names a file, function, command or setting that no longer exists
3. Dispatch the reviewers **in parallel, in the background**: for the core audit, the three always-on reviewers and each conditional reviewer whose trigger fired; for the other kinds, as the table says. Say in one line which reviewers run, which are skipped, and why. When a trigger is borderline, skip the reviewer under Lean and Balanced and run it under Throughput. Brief each with the key numbers, the evidence paths, and (for the core audit) the changed files in its area. They're read-only and cite their evidence. Give them step 2's results so none re-runs the suite, and have reviewers that drive a UI start from the screenshots. When the UX reviewer and the user tester would both drive the same UI, fold the user tester's three personas into the UX reviewer's live pass: live UI use is the most token-hungry thing an agent does. If the project's agents aren't available as agent types, run general-purpose agents told to follow the matching agent file. 
4. `triage` merges the findings and scores them, assigns Tier and Est. time, and regroups the batches.
5. Report: the headline numbers (including the usage report's top line), any usage finding that would change an agent's model or effort as a decision for the user (it trades quality for usage), the top 5 Ready items, the batches, and the decisions the user needs to make. Update the "Last audit" line (date, commit and kind), and commit `BACKLOG.md` (in a PR, for Team and Enterprise).

**`/iterate`**: everyday, and batch-first.

| Command | Does |
|---|---|
| `/iterate` | Run `B1`, the batch that holds the top Ready item |
| `/iterate B3` | Run a named batch |
| `/iterate ITEM-ID` | Run the batch that contains that item |
| `/iterate ITEM-ID solo` | Run just that one item |
| `/iterate B2 without ID` | Run a batch minus some items |
| `/iterate B3 on Hard` | Raise a batch's work tier |

0. **Intake.** Users send new requests mid-run. Before picking a batch (and whenever new requests arrive), write each one as a goal, with the user's own solution ideas recorded as context rather than requirements. Merge any request that overlaps a queued item into that item instead of adding a near-duplicate; move superseded items to Rejected. Then `triage` regroups the batches and refreshes priorities across the whole Ready list, keeping anything the user explicitly pinned. Tell the user in a line or two what was merged, added or re-ordered.
1. **Pick.** State the batch, its category, work tier, items, reviews and Est. time. Move the items to In progress. Regroup first if the batches are stale. Team and Enterprise: create a branch named `batch/<id>-<slug>`.
2. **Implement.** Brief **one** implementer of the batch's work tier with every item's row and proposal. It works through them as separate, isolated edits and runs the tests once at the end. **Never run two implementers on the same working tree**: they overwrite each other's uncommitted edits. Parallel work needs isolated working copies (Git worktrees, or separate checkouts; Appendix K). An item that turns out riskier than its category goes back to Ready.
3. **Test.** The full tests pass, run once with screenshots so the user tester reads them rather than running the suite again. Put the test and bench results in every reviewer's brief. If `bench --compare` fails, run the set-aside A/B test to tell a real regression from machine noise.
4. **Review.** Run the category's reviews once, briefing each reviewer with the whole item list. Always name the change review's level (Appendix I explains why on the reference platform): `low` for `ui` and `tooling`, `medium` for the other categories, `high` only for Deep items or when the user asks (it costs several times as much). These are the Balanced levels; the usage profile moves them (section 1).
5. **Triage.**
   - Blockers go back to **the same implementer**, resumed with a message so it keeps its context. If that agent has already finished, start a fresh one and give it the full context.
   - Re-run steps 3–4 after the fix.
   - Drop an item from the batch rather than hold up the rest.
   - Everything else waits for the single `triage` call in step 7.
6. **Ratchet the baseline** only after a genuine improvement.
7. **Finish.**
   - Give each item its own Done row in `BACKLOG_DONE.md`. Record the batch's actual time once, on its first item.
   - Commit per item when the diffs separate cleanly. Otherwise, make one commit that lists every ID.
   - **Solo:** publish (the hook gates it), then deploy or republish if the product changed. Where there's nothing to publish to (no remote, or no version-control tool), run the publish gate's checks before deploying, and take a snapshot if the project uses them.
   - **Team and Enterprise:** open a PR with the batch table and test and bench results, let CI run, and hand it to the reviewers. Merge only if the profile allows it; Enterprise never self-merges.
   - Call `triage` **once**: it files the reviews' non-blocking findings and regroups the remaining items. Skip it if there's nothing to add and nothing was dropped.
   - **Report** to the user as a table (ID, Tier, Est. time, short description of the change), then actual against estimated time, the test and bench numbers, the PR link if any, and the next batch.

**When an agent hits a usage or rate limit** (in any skill): check the real clock first (`date`); task notifications can arrive late, and there's no other reliable clock. If the reset time has passed, check for half-finished edits and resume the same agent. If it's still in the future, tell the user the real reset time and how long that is from now, and carry on with work that doesn't need that agent. Retry a 429 that gives no reset time once before treating it as real. Quote the error as given; don't call a limit model-specific unless it says so.

**Keep the orchestrating session lean** (in any skill). It re-reads its whole context on every turn, which makes it the biggest single cost in a long run: read parts of large files (a search, or a read of a line range) rather than whole ones, open images only when you need to see them, point agents at files rather than pasting them, and ask for short reports. Start agents fresh with a brief rather than as forks (copies of the session), since a fork starts with the whole session's context. After a compaction, rebuild state from the backlog's In progress rows, the working copy's status (`git status` in Git) and the list of running agents. When the user runs `/iterate` by hand, suggest clearing the session's context between batches: the backlog and version control hold the state. If an agent stops at its turn cap, resume it rather than starting over.

**`/devmanual`**: the developers' guide on demand, so nobody has to read `docs/DEV_CYCLE.md` to remember how the project works. With no argument it prints a short version: the framework size and its life cycle; only the commands that exist in this project; the current state (Standard and Full: the batch in progress and the next one; Light: the open items in `docs/TODO.md`, or when the data was last refreshed); and the upgrade trigger. `/devmanual full` prints the whole of `DEV_CYCLE.md` (or, in a Light project without one, README's "Working on this" section). It reads that doc, the backlog or `docs/TODO.md`, and the version-control history if there is one, and changes nothing. It's part of the docs that move together (Phase 17): a workflow change updates it in the same change.

**`/autoiterate`**: the same cycle, looped. `/iterate` runs one batch and stops; `/autoiterate` repeats Intake → Pick → the full `/iterate` pipeline → a two-line report, batch after batch, without waiting for the user. Every quality gate still applies to every batch.
- **Turning it off:** `/autoiterate stop` finishes the batch in flight (through its commit and publish or PR) and stops; `/autoiterate stop now` stops at the next safe point, committing what passes the gates or setting the rest aside, never leaving half-finished edits. When it runs under a loop or scheduler, both also cancel the scheduled wake-up so it can't restart. No separate off command is needed.
- **Stop only when** the Ready list is empty or wholly blocked on the user; a decision only the user can make blocks the next useful work (ask once, and keep working on batches that don't depend on it); something is broken that one fix round couldn't repair; the user says stop; or an argument limit is reached (`/autoiterate 3` for three batches, `/autoiterate until ID`). On stopping, report every batch shipped in the run.
- **Under the Lean profile,** treat a Deep `solo` batch or an A/B experiment (under Balanced, only an A/B experiment) as needing the user's go-ahead unless they've already given it for that item (in the reference build, one A/B experiment used about a fifth of all usage to that point). Ask once, and carry on with other batches meanwhile.
- **Never end a turn idle:** either an agent or command is in flight (its notification resumes the session), a wake-up is scheduled, or the loop has stopped for one of the reasons above.
- **Session limits:** apply the limit rule above. For unattended work, run it under the platform's loop or scheduler (Appendix I): when the reset is in the future, it schedules its own wake-up for the reset time plus a couple of minutes (chaining wake-ups if the platform caps how far ahead one can be), re-checks the clock on waking and resumes. Plain `/autoiterate` loops just as well but needs a nudge after a limit.
- **Team and Enterprise:** each batch ends in its PR per the version-control flow, and the loop carries on with batches that don't depend on an unmerged PR (branching from the main line). It stops when the next useful batch depends on a PR still waiting for human review.

Commit.

---

## Phase 13: Model routing and token efficiency 🚦 (confirm with the user)

**Light:** the dev plan already names the session's and the security reviewer's model tier and effort, confirmed at the Phase 2 gate. Pin them in the project settings and the agent file, and run the rest of this phase only when an implementer agent is added.

By now the project type, the architecture (Phase 5), the dev cycle (Phases 10 to 12) and the usage profile are known. Use them to choose each role's model and effort for *this* project, rather than copying a default. The policy: *use the strongest model only where it's clearly better, and send routine or checklist work to cheaper models or lower effort.*

1. **Sort the platform's models into tiers:** **strong** (the best reasoning, the dearest), **standard**, and **light** (fast and cheap). Check the current models, prices and caching rules; Appendix I binds the tiers for each platform.
2. **Profile each role:** every agent, the main session, triage and the change-review step.
   - How hard is its reasoning *in this project*? A numerical core, concurrency, money, security or data-migration logic is hard; checklists, copy and styling aren't.
   - What does a miss cost? A missed data-loss or security bug costs more than a missed spacing issue.
   - How much does it read against what it writes, and how often does it run per batch?
   - Does it wait between turns (for benchmarks, or for reviews before a fix round)? That sets its cache lifetime.
3. **Give each role a tier and an effort level** (low, medium or high), with a one-line reason, then apply the usage profile's changes (section 1). Start from the reference routing below, and move a role only when the profile of its work says so. For example, an analysis project's data reviewer does its hardest reasoning and belongs on the strong tier, while a static brochure site may need no strong-tier implementer at all.
4. 🚦 Show the routing table with its reasons, and confirm it with the user. Record the table in the project instructions file and the reasons in the Decisions log. Pin each agent's model tier explicitly rather than letting it inherit the session's, so a strong-tier agent stays strong in a standard-tier session.

**Reference routing** (the reference build: Balanced profile, 2026-09; Appendix I shows it as Claude Code settings). A starting point, not a default:

| Setting | Reference build |
|---|---|
| Session default | Standard tier at **medium** effort, pinned in the project settings, compacting at 200K tokens so a long run doesn't grow its context without limit |
| Raising effort | In the main session, only for hard reasoning, then back to medium. Avoid the top effort levels. |
| Strong, medium | domain-correctness, perf, security and evaluation reviewers, and `implementer-hard` |
| Strong, high | `implementer-deep` only |
| Standard, medium | UX, product, code-quality, compliance, infra and data reviewers, `implementer`, `triage` |
| Standard, low | efficiency auditor, user tester, accessibility reviewer |
| Light | Only where it's proven good enough (for example mechanical formatting or lookups), after a trial |
| Agent guards | A **turn cap** on every agent, set well above a normal run, as a runaway guard. A **one-hour cache lifetime** on the implementers and on any agent that waits more than five minutes between turns (benchmarks, reviews before a fix round), where the platform's default cache is shorter: each expiry re-caches the agent's whole context. |
| Change review | Always with a level: `low` for `ui` and `tooling`, `medium` otherwise, `high` for Deep items or on request |

- **Judge cost per finished task, not per token.** In an agentic session most of the cost is re-reading cached context, and on some platforms a cached re-read costs the same per token on the strong and standard tiers (Appendix I notes the reference platform's pricing; check current prices). A stronger model that finishes in fewer turns and fix rounds can cost less. The big levers are context size, cache expiry, duplicated work and agent fan-out, before the model.
- Change agent settings **one level at a time**, with a reason. Record the new setting in the project instructions file, and the date and reason in the Decisions log.
- **Keep the always-loaded context small** (Phase 6). The project instructions file, and everything else loaded at session start, is re-read on every turn of every session.
- Switch models at the **start** of a session, because prompt caching is per model.
- Let batching save the cost: one cycle per batch, not per item. Run `/audit` rarely.
- Don't spawn agents for work that takes a couple of direct tool calls.
- Durable project preferences go in the checked-in docs. Personal preferences go in the platform's personal memory.

**Usage report.** Build `tools/usage_report.py` (a plain script, no model calls). It reads the project's session records from where the platform keeps them (Appendix I gives the reference platform's location and format): one record per main session and one per agent run, each naming the agent's type. It deduplicates each message's usage by message id, and prices input, cache writes (by their lifetime), cache reads and output by model. It prints, per main session and agent type: runs, share of the total, cost and turns per run, peak context, and the share spent re-caching after idle gaps; then the most expensive runs; then the size, in lines and bytes, of every instruction file loaded at session start; then findings in the audit format (`USAGE-###`, area `usage`) for a main session past the compaction window, instruction files over their size budget (Phase 6), agents re-caching after idle gaps, change reviews run above medium, forks started from a large context, and agent types whose runs grew much longer or costlier since the last report. `--since last` covers the period since the last `--save`, which appends a summary to a usage-history file (Appendix I gives its path). It uses list prices as a proxy for plan usage, and says so in its output. It only sees sessions run on that machine: Team and Enterprise members run it on their own machines, or use the organisation's usage reporting where it has one. If the platform keeps no readable session records, use its usage dashboard or API usage reports instead, and record the gap in the Decisions log. Take the first usage snapshot now, so the first retrospective has a baseline. Light projects skip the usage report until they grow to Standard.

Commit the project instructions file, the agents' settings and the usage report.

---

## Phase 14: Hooks and CI: deterministic enforcement

**Local hooks** (all profiles): scripts kept in the repo, wired to the platform's hook events in the project settings (templates in Appendix G; Appendix I has the reference wiring). A Light project uses `after_edit.py` and `protect_secrets.py`, and a publish gate that runs the tests only, unless its dev plan adds benchmarks. Where there's nothing to publish to (a repository with no remote, snapshots, or none) there's no publish command to hook: the ship routine runs the gate's checks before deploying, or a hook gates the deploy command if there is one.

| Hook | Runs | What it does |
|---|---|---|
| `after_edit.py` | After a file edit | If a watched file changed (source, test harness, bench scenarios), runs the formatter and linter on it (if the project has them), then the **quick** check. Reports the output as a failure the agent must fix. |
| `before_publish.py` | Before a shell command | On a real publish command **of this repository** (`git push` in Git, matching `git [global options] push` but not `git stash push` or "push" inside a message; `svn commit` in Subversion; Appendix K), runs the full tests and `bench --compare`, and blocks the publish if either fails. It works out the target from any `cd`/`Set-Location` earlier in the command and from options such as `git -C <dir>`, then asks the version-control tool which repository that is. Publishes of other repositories made from the same session pass through, and an undeterminable target is gated (fail safe). |
| `protect_baseline.py` | Before a file edit | Denies direct edits to `bench/baseline.json`. The baseline only changes through `--baseline`. |
| `protect_secrets.py` (all profiles) | Before a file edit or a shell command | Denies writing likely secrets (key patterns, high-entropy tokens), and reading `.env` or credential files into context. |

**CI** (Team and Enterprise) runs the same commands, so what's verified locally and what's verified in CI can't drift. Enterprise adds the security scans and required checks. Never bypass a gate (no `--no-verify`). When a gate blocks, find the root cause. Give gate hooks a generous timeout (at least 2× the time the tests and benchmarks take together): on some platforms, including the reference one, a hook that times out doesn't block, so the publish would go through unchecked.

Commit.

---

## Phase 15: Operations (Team and Enterprise, for hosted services)

1. **Observability:** structured logging, error tracking, key metrics (the performance budgets and the business KPIs) and, for Enterprise, tracing.
2. **SLOs and alerts** from the reliability targets in the project instructions file.
3. **Runbooks** (`docs/runbooks/`): deploy, roll back, rotate a secret, restore from backup, respond to an incident.
4. **Cost:** a budget alert on the cloud account, and a cost-per-unit metric where it's meaningful.
5. **Backups and restore**, tested once.
6. Add infra/SRE findings to the backlog as `infra` items. Commit.

---

## Phase 16: First audit, first batch, retrospective

Standard and Full. **Light:** run the change review and the security reviewer once over v1, fix the blockers, and hold a short retrospective: does the size still fit, and has any trigger fired or any piece of process gone unused (step 4)? **None:** skip this phase.

1. Run the full "before every change ships" routine once on the current state, and fix the blockers.
2. 🚦 Ask before the first `/audit`, since it costs the most: it's a core audit that counts the whole project as changed, so every conditional reviewer the project has runs, except the efficiency auditor and the product designer, which wait for the first `/audit full`. Run it, then report the top 5 items and the batches.
3. Run the first `/iterate`. Watch for:
   - scope creep in the implementer's diff
   - noise in the benchmark gate
   - findings that repeat known quirks (add these to the agents' quirk lists)
4. **Retrospective with the user.** Look at actual against estimated time, token spend (from the usage report) against the usage profile, which agents found real problems and which produced noise, whether the conditional reviewers' triggers fit (tighten a trigger whose reviewer found only noise; loosen one where a problem its reviewer would have caught got through), and whether the scale profile and the framework size still fit. **Prune the process:** a doc nobody has read or needed, an agent whose findings were all noise, a skill nobody runs. Propose folding each into something that stays, or removing it, and record it as a dev-plan change with the trigger that would bring it back. Process creeps up by default, so this check runs at every retrospective. Tune the model and effort settings and the batch caps **one level at a time**, and record each new setting in the project instructions file and its date and reason in the Decisions log.
5. Commit.

---

## Phase 17: Handoff

1. Write `docs/USER_GUIDE.md` (with the docs writer, where the project has one): everything a user needs, in plain language and without code — how to use every feature, what the outputs mean, limits and error messages. Link it from the product's own help where there is one. For None and Light, a README section can be the user guide.

   Then write the developers' guide, **sized to the project**, and teach only its own life cycle: None and Light projects don't get `/audit`, `/iterate` or `/autoiterate`, and their docs don't mention them.
   - **None:** a "Working on this" section in README: how to run it, how to change it, and when it would be worth more process (the upgrade trigger). Before delivering, run the one-off security pass with Appendix C2's checklist (ground rule 8).
   - **Light:** a "Working on this" section in README: the life cycle, the commands that exist, where things live, the recurring chores, and the upgrade trigger. It moves to a `docs/DEV_CYCLE.md` of about half a page only when it outgrows the README.
   - **Standard and Full:** `docs/DEV_CYCLE.md` in full. It covers:
     - a table of the commands
     - the backlog columns (Priority with its class, Tier, estimated and actual time), and why class comes before score
     - the batch categories and why the caps exist
     - the steps of `/iterate`, `/autoiterate` and `/audit`, including intake and the limit rule
     - the version control and its PR flow for this profile
     - how to choose what to iterate on (by theme, cost, dependencies, fixes before features)
     - the A/B experiment convention
     - how to keep usage down
     - three worked examples using real batches from the backlog
     - the upgrade and downgrade triggers for the framework size
2. Make sure the project instructions file, `DEV_CYCLE.md`, `ARCHITECTURE.md`, the user guide, the domain-logic reference, the operations notes, the design guide, the skills (including `/devmanual`), the agents, CI, the runbooks and `README.md` all agree, for whichever of them the project has. **Every workflow change updates all of them in the same commit or PR.**
3. Tick every phase in `docs/PROJECT_PROGRESS.md`. Once the development loop is live, mark it and this spec as archived history.
4. Give the user a brief summary of the project's life cycle, with its commands (`/devmanual` repeats it at any time) and, for Standard and Full, the first batch to run.

### Definition of done

Each item is marked with the smallest framework size it applies to: **[Light+]** for Light, Standard and Full; **[Standard+]** for Standard and Full; **[Full]** for Full only. Unmarked items apply to every size, None included. Items marked (T) apply to the Team profile, (E) to Enterprise, and (T/E) to both. A project isn't incomplete for lacking what its size leaves out, or what a trigger hasn't yet added, as long as its dev plan records each omission and its trigger.

- [ ] Problem analysis, project type, scale profile, usage profile, comment level, stack, version control, non-functional requirements and pillars confirmed (in the Decisions log)
- [ ] The framework size chosen, and the dev plan written: phases, agents, skills and gates, each with a reason, and a trigger for each thing left out; within the process budget (None and Light: the framework work well under v1's)
- [ ] A working v1, shown to the user
- [ ] User interfaces: the visual direction confirmed. [Light+] Design tokens in place, and reviews check against them. [Standard+] `docs/DESIGN.md` (Light: the direction at the top of the tokens file, and the guide once its trigger fires)
- [ ] None only: a one-off security pass over the deliverable, and a README that says what it is, how to run it, and how to work on it
- [ ] [Light+] Version control as chosen, with author identity and an ignore list (snapshots or none: the reason in the Decisions log); licence and header convention; remote, CI and deployment as agreed; main-line protection (T/E) and code owners (E); environments as infrastructure-as-code (E)
- [ ] [Light+] Code conventions recorded in the project instructions file (style guide, formatter and linter where the stack allows, naming, test layout, comment level), and the code follows them
- [ ] [Light+] The system is deterministic, steppable and inspectable from tests. [Standard+] `docs/ARCHITECTURE.md` accurate; threat model, and data classification (T/E). Light: a "How it's built" paragraph in README and security lines in the project instructions file, until their triggers fire
- [ ] [Light+] The project instructions file with files, the ship routine, numeric targets, invariants, security and compliance, pillars, workflow, model and effort, conventions and a "Read when" index, within its size budget. Light: only the sections with something project-specific to say, in about 50 lines
- [ ] [Light+] Tests (full and quick; screens for user interfaces) of the core logic and of the whole thing end to end pass, with a secret scan and a dependency audit. [Standard+] Invariant tests and accessibility checks (Light: on their triggers); the cross-platform matrix; security and dependency scans in CI (T/E)
- [ ] [Light+] The security reviewer and the change review in place. Light: the session implements (an implementer agent only on its trigger), and `docs/TODO.md` for known issues and ideas
- [ ] [Light+] Hooks: formatter, linter and quick check, the secrets guard, and a publish gate that runs the tests. [Standard+] The benchmark comparison in the publish gate, and the baseline guard; CI mirrors them (T/E)
- [ ] [Light+] Model routing chosen for the project, with reasons, and confirmed (Light: the session's and the security reviewer's, in the dev plan); the session's effort and compaction window pinned
- [ ] [Light+] A developers' guide sized to the project. [Standard+] `/devmanual` working and `docs/DEV_CYCLE.md` in full. Light: README's "Working on this" section (`/devmanual` and `docs/DEV_CYCLE.md` on their triggers)
- [ ] [Standard+] Benchmarks with a median-of-N baseline, a 5% compare gate and a budget report (Light: only if the dev plan adds them)
- [ ] [Standard+] Environment helper; browser tool with a fallback; allowed-tool list (E)
- [ ] [Standard+] Read-only reviewers with evidence rules and quirk lists: the three always-on reviewers (domain correctness, code quality and **the security reviewer**), and only the conditional ones the project needs, each with its audit trigger; the three implementers (Routine, Hard, Deep); triage
- [ ] [Standard+] The first audit includes a security baseline; secret and dependency scans pass
- [ ] [Standard+] `BACKLOG.md` with the Batches, priority class and score, Tier, estimated and actual time columns, plus a mechanical consistency check
- [ ] [Standard+] `/audit`, `/iterate` and `/autoiterate` working end to end, batch-first, following the profile's version-control flow
- [ ] [Standard+] The usage report, run by `/audit`, with a first usage snapshot saved; agent turn caps and cache lifetimes set
- [ ] [Full] The scale profile's extra reviewers; CI as the authority for the gates
- [ ] [Full] Observability, SLOs, runbooks and cost alerts (hosted services)
- [ ] [Standard+] First audit, first batch shipped, retrospective done, settings tuned, unused process pruned. Light: v1 reviewed once, and a short retrospective on whether the size fits and what to add or prune
- [ ] [Standard+] `docs/USER_GUIDE.md`, the domain-logic reference and `docs/OPERATIONS.md` written and reviewed. Light: README covers using, running and deploying it (a data view keeps its metric dictionary), and each doc is written when its trigger fires. [Light+] All docs consistent. Every size: a summary of the project's life cycle given to the user

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
| **Data view / dashboard page** (for example a one-page view for a marketing team) | A small script that turns the source export into one self-contained HTML page, with the data aggregated and embedded; the organisation's BI tool if it has one | Email, SharePoint, Google Drive or an intranet page; works offline and prints | The build script run on a fixture export; a reconciliation check; a headless-browser screenshot | metric definitions, aggregation, filters, time zones and periods, chart choice | totals reconcile with the source; the same data gives the same numbers; filters never silently drop rows; missing data shows as "no data", never 0 | page size and filter time on the largest expected export; refresh time |
| **Automation / spreadsheet** | Apps Script, Python + openpyxl, workflow tools | The user's workspace | Fixture-based tests | formula logic, dates, currencies | totals reconcile; reruns idempotent | runtime on large inputs |

Rename the categories and agents to fit the project. For example, the reference project's `sim` category is `core` here, and its physics reviewer is the domain-correctness reviewer.

**Typical framework size** (Phase 2 decides): a one-off analysis or script is usually None; a data view or dashboard, an automation or a small internal tool, Light; a product, service, integration, library or game that will keep changing, Standard; and any of those at Team or Enterprise scale, regulated, or hosted with uptime targets, Full.

**Data views and dashboards** (for example single HTML pages for marketing people). Usually sized Light.

- **Metric dictionary:** `docs/METRICS.md` is the domain-logic reference. For each metric: its name, what it means in plain language, the formula, the source fields, the filters, and who owns the definition. The page's labels use the same names.
- **Refresh:** one documented step the audience can run themselves (for example, save the new export into a folder and run one command, or open one file). The page shows the date its data runs to. A project skill such as `/refresh` does the same for the developer.
- **Invariants,** tested on every build: totals reconcile with the source; the same data gives the same numbers; filters never silently drop rows (show a count of what each filter excludes); missing data shows as "no data", never as 0.
- **Privacy:** aggregate before embedding. A shipped file can be forwarded to anyone, so it holds no personal data and no group small enough to identify a person. Row-level detail stays in the source system, behind its own access controls.
- **Chart quality,** checked in review: the chart fits the question (a line for a trend, bars for a comparison, a table when people need exact numbers); a colour-blind-safe palette; plain labels with units; bars that start at zero; direct labels rather than a legend where possible. The look follows `docs/DESIGN.md`, at the compact density a dashboard needs.
- **Delivery:** it works as an email attachment, from SharePoint or Drive, offline, and printed (a print stylesheet), and makes no external requests that the audience's network might block.

---

## Appendix B: Project instructions file skeleton

Write it to the platform's instructions file (Appendix I).

```markdown
# <Project name>

<One paragraph: what it is, for whom, stack, where it runs. Scale profile: Solo | Team | Enterprise. Framework size: Light | Standard | Full (the dev plan is in `docs/PROJECT_PROGRESS.md`). `docs/ARCHITECTURE.md` explains the code; `docs/DEV_CYCLE.md` explains the dev loop (Light: README, until those exist).>

<!-- Keep this file within about 150 lines and 10 KB (Light: about 50 lines): rules and numbers here, their reasons in the Decisions log, detail in the docs listed under "Read when". Leave out any section with nothing project-specific to say, and list only the docs that exist. -->

## Files
- `<main source>`: …
- `tests/…`: one command; `--quick`, `--screens`.
- `bench/…`: `bench/baseline.json` is committed; results are ignored.
- `tools/…`: environment helper, hosted-copy build.
- <the platform's agent, skill and hook folders, project settings and tool-server config (Appendix I)>, CI config
- `BACKLOG.md`: batches and items, triaged. (Light: `docs/TODO.md`.)

## Read when
- Changing <the core logic> → `docs/<DOMAIN>.md` · UI work → `docs/DESIGN.md` · How the code is laid out → `docs/ARCHITECTURE.md` · Shipping, batches, commands → `docs/DEV_CYCLE.md`
- Deploying or rolling back → `docs/OPERATIONS.md` · A security-sensitive change → `docs/THREAT_MODEL.md` · Why a setting is what it is → the Decisions log in `docs/PROJECT_PROGRESS.md`

## Before every change ships
<!-- Light: steps 1 and 3, the security reviewer from step 5, then step 6. -->
1. Tests: every check passes.
2. Bench `--compare` when speed could change: no regression over 5%.
3. The change review (<the platform's command>), and fix what it finds.
4. The user-tester agent checks the change.
5. Domain-correctness reviewer if the core logic changed. **Security reviewer** if the change touches a sensitive area (authentication, input handling, secrets, dependencies, headers/CSP, CI/CD, infrastructure, data access, LLM prompts or tools).
6. Solo: publish (hook-gated) and deploy. Team/Enterprise: open a PR; CI and a person gate the merge.

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
- <Standard/Full:> Audit (rare) · Iterate by batch (see DEV_CYCLE.md) · Bench run / --compare / --baseline (only on genuine improvement)
- <Light: ask for a change; implement, test, review, commit; chores via the project skill, if there is one. Add a piece of process only when its trigger in the dev plan fires.>
- Version control: <Git / other / snapshots / none>; flow: <profile's flow>. A/B experiments in two isolated working copies. Unattended runs: off unless opted in.
### Model & effort
- Usage profile: <Lean / Balanced / Throughput>, chosen <date> because <reason>.
- <Phase 13 routing table: one line per tier and effort, with the roles on it; the session's effort and compaction window; the change-review levels. Reasons and dates go in the Decisions log.>
### Documentation
- Every change that alters behaviour updates the doc that describes it in the same change: USER_GUIDE (users), <DOMAIN>.md (the rules), ARCHITECTURE (code), DESIGN (the look and feel, for user interfaces), OPERATIONS (deploy and rollback), THREAT_MODEL (entry points), README (the short version). The docs writer checks them all in /audit (Light: the change review does).
## Conventions
- Header: `Copyright (c) <year> <Owner>. All rights reserved.` (or the licence line), then a line or two on what the file holds.
- Code: <style guide>; <formatter and linter>, run by the after-edit hook. Names say what things are, with units. Tests: <layout>, names that state the behaviour, arrange/act/assert. Errors name what failed and the values.
- Comment level: <Agents-first / Standard / Human-maintained>, chosen <date> because <reason>. Comments say why, not what; update them with the code.
- Commits authored as <Name> <email> (set for this repository only). <Style, platform and input conventions.> Keep README in step. All randomness through `rand()`.
```

---

## Appendix C: Agent templates

Each template has two parts: its **settings**, which the platform writes in its own format (Appendix I shows the reference platform's), and its **instructions**, which go in the agent's file as they are. Tool names are neutral: *read*, *search*, *shell*, *edit* (editing and writing files) and the *browser tool*. Model tiers and effort levels are starting points; Phase 13 sets them for the project.

**Reviewer (read-only):**

| Setting | Value |
|---|---|
| Name | `<role>` |
| Description | <what it reviews and when to use it (in /audit, after X changes)>. Read-only. |
| Model tier | Standard; strong for deep-reasoning roles. Pin it explicitly. |
| Effort | Medium; low for observe-and-report roles |
| Turn cap | 60: a runaway guard, well above a normal run |
| Tools | Read, search, shell; plus the browser tool for UI roles. Never edit. |

```markdown
You review <facet> of <Project>. Read the project instructions file (targets, pillars, security) and `docs/ARCHITECTURE.md` first.
You never edit, commit, publish or deploy, and you never use production credentials. Throwaway experiments go in a temp folder outside the repo.

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

| Setting | Value |
|---|---|
| Name | `docs-writer` |
| Description | Writes and audits <Project>'s documentation - user guide, domain-logic reference, operations notes, threat model - and in /audit checks the docs the drift check flags against the code. Edits documentation only. |
| Model tier | Standard |
| Effort | Medium |
| Turn cap | 60 |
| Tools | Read, search, shell, edit; plus the browser tool to use a UI |

```markdown
You write and maintain <Project>'s docs. Read the project instructions file and `docs/ARCHITECTURE.md` first.
You edit only README and `docs/`; never code, tests, tools or agents; never commit, publish or deploy.

## Writing
Confirm every claim in the code or the running product. Plain sentences for a reader who starts cold; tables for reference; one concern per doc, linking instead of duplicating. Domain docs need their expert's review before they ship.

## Auditing
Check the docs the audit's drift check flagged (or every doc, in a full audit) against the current code and product. Report each mismatch, and each feature that shipped without docs, as a DOC-### finding with evidence (doc line vs code or observation).
```

**Implementer** (three copies, one per work tier: `implementer` for Routine, `implementer-hard` for Hard, `implementer-deep` for Deep):

| Setting | Value |
|---|---|
| Name | `implementer`, `implementer-hard` or `implementer-deep` |
| Description | Implements a <Project> BACKLOG.md batch (or a single item) as the smallest reasonable changes, one isolated edit per item. <Work tier scope>. Use from /iterate. |
| Model tier and effort | From Phase 13 (reference: standard and medium, strong and medium, strong and high) |
| Turn cap | 100; 150 for `implementer-hard`, 200 for `implementer-deep` |
| Tools | Read, search, shell, edit |
| Cache lifetime | One hour: it waits through test runs and reviews before a fix round |

```markdown
You implement backlog items for <Project>. Read the project instructions file first.
You implement and test. You never commit, publish, merge or deploy.

## What to do
1. Make the smallest reasonable change that delivers each item, following the project's conventions and security rules. Write to the code conventions and comment level in the project instructions file: clear names, the language's idioms, comments that say why, and doc comments at the chosen level. Update any comment your change makes wrong. For UI work, read `docs/DESIGN.md` first (or, where there isn't one yet, the visual direction at the top of the tokens file) and use its tokens.
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

Every project sized Light or above gets this agent, whatever its scale; a deliverable sized None gets one pass with this checklist before it's delivered. Solo projects run the core checklist; Team and Enterprise add the CI scanners and compliance mapping.

| Setting | Value |
|---|---|
| Name | `security-reviewer` |
| Description | Dedicated security specialist for <Project>. Reviews the whole system in /audit, and any change that touches a sensitive area, against the threat model. Covers authentication and authorisation, input handling and injection, secrets, dependencies and supply chain, browser security headers, CI/CD and infrastructure, data protection, and LLM-specific risks. Read-only. |
| Model tier | Strong |
| Effort | Medium |
| Turn cap | 60 |
| Tools | Read, search, shell; plus the browser tool for web apps. Never edit. |

```markdown
You are the security reviewer for <Project>. Read the project instructions file (the security & compliance section) and `docs/THREAT_MODEL.md`, if the project has one, first. Your job is to find real, exploitable weaknesses and risky patterns, backed by evidence, and to keep the threat model current.

## Rules of engagement
- Read-only. You never edit, commit, publish or deploy.
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
- **Secrets:** none in the repo, version-control history, logs, client bundles, error messages or agent context. Keys can be rotated and have least privilege.
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
- **Impact:** 1–5 (critical = 5, high = 4, medium = 3, low = 1–2)   **Dev effort:** 1–5   **Class:** P1 for critical and high, P4 for medium and low (Phase 11)
- **Proposal:** the fix, plus a regression test that would catch it

End with:
- a threat-model delta: new assets, entry points or threats to add to `docs/THREAT_MODEL.md` (or, in a Light project without one, to the security lines in the project instructions file, saying if the threat model's trigger has now fired)
- anything that needs the user's decision, such as accepting a risk or a compliance question

No evidence, no finding. Flag critical and high findings to the user straight away, as well as adding them to the backlog.
```

For a change review rather than an audit, the same agent reviews only the diff and its data flows, and reports blockers first.

## Appendix D: `triage` rules

| Setting | Value |
|---|---|
| Name | `triage` |
| Description | Turns audit and review findings into BACKLOG.md. Discards findings without evidence, merges duplicates, scores priority, assigns Tier and Est. time, and groups Ready items into batches. Use at the end of /audit and /iterate. |
| Model tier | Standard |
| Effort | Medium |
| Turn cap | 30 |
| Tools | Read, search, edit |

```markdown
Rules:
1. Discard findings with no concrete evidence. List them at the end of your reply, not in the backlog.
2. Merge duplicates: keep the clearest title, combine the evidence.
3. Priority is lexicographic: the class first, then the score (impact ÷ effort, two decimals) within the class, written as `P<class> <score>`. Break ties with broken or risky behaviour first, then smaller changes. Classes:
   - P1 safety and security blockers: critical or high security findings, exposed secrets, a safety gate that lets bad changes through, anything that could harm users or others. Needs an exploit path, a scan result or a failing check.
   - P2 correctness and data-loss blockers: wrong results, lost or corrupted data, a broken invariant, crashes or hangs in normal use. Needs a failing test or invariant, or a reproduction.
   - P3 user-visible regressions: worked before and no longer does, or got measurably worse (matches a Done item, a benchmark regression, a budget no longer met). Needs the before and after, or the Done item.
   - P4 high-impact improvements: impact 4 or 5, and any medium or low security finding or conformance gap, whatever its impact.
   - P5 efficiency and polish: everything else.
   Set the class from the kind of problem and its evidence, never from the impact score. A finding claimed for P1 to P3 without that evidence drops to P4 or P5; say so in your report. Keep a user's pin, and report it. **P1 items go to the top of Ready as a `security` batch (or `solo`), and are flagged to the user.**
4. Check the pillars and the security rules in the project instructions file. A finding that breaks either goes to Rejected, with the reason.
5. Preserve status. Never delete or reorder In progress, Done or Rejected. A finding that matches a Ready item updates its evidence. A finding that matches a Done item is a regression: add it as new, with a note. Done items are in BACKLOG_DONE.md: search it, don't read it whole.
6. IDs are AREA-###, numbered after the highest existing number for that area.
7. Sort Ready by class, then score. One line per row, with evidence as short pointers. Work in one pass: read BACKLOG.md once, then a few edits or a single rewrite, without re-reading it to check.
7a. Usage findings (USAGE-###, from the usage report) go in the tooling category. One that would change an agent's model or effort trades quality for usage: mark it as needing the user's decision and keep it out of every batch until they decide.
8. Tier (the work tier):
   - Deep: <the project's hardest class of work>, A/B experiments, irreversible changes
   - Hard: core, perf or security area, the logic of the safety gates (hooks, the build, the test runner's pass/fail logic), or effort 2 or more
   - Routine: everything else at effort 1
   Est. time: Routine effort 1, 10–20 min; Hard effort 1, 15–25; effort 2, 20–35; effort 3 or Deep, 35–90+.
9. Batches. Regroup ALL Ready items on every run. Number batches in the order of their highest-priority item, so B1 holds the top item. Lower-class items may ride along in a batch of the same category and tier, but never move a batch ahead.
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
     - Ready is sorted by class, then score, and no P1 to P3 item lacks its class's evidence
Finish with: findings received, kept, merged and discarded; any class changes (dropped for missing evidence, or pinned by the user); the count of Ready items per class; the top 5 Ready items; and the batches (ID, category, item count, Est. time).
```

---

## Appendix E: `BACKLOG.md` template

```markdown
# Backlog

_Last audit: YYYY-MM-DD, at <commit>, <core | full | focus>_

<Note on the columns: Tier, Est. time and Batch. See docs/DEV_CYCLE.md.>

## Batches (regenerated by triage; /iterate runs one batch at a time)
| Batch | Category | Tier | Items | Reviews | Est. time | Why grouped |
|-------|----------|------|-------|---------|-----------|-------------|

## Ready (sorted by priority: class P1–P5 first, then impact ÷ effort; for example `P1 1.67`)
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

Each skill's settings, which the platform writes in its own format (Appendix I shows the reference platform's):

| Skill | Description | Arguments |
|---|---|---|
| `iterate` | Take the next batch of Ready items from <Project>'s BACKLOG.md (or a batch, item or items the user names) through the full pipeline once: implement, test, benchmark, review, triage, then ship per the project's version-control flow. Use when the user says iterate, "do the next batch", or names a batch or item ID. | A batch ID, an item ID, "ID solo", or nothing |
| `autoiterate` | Keep running <Project>'s /iterate cycle batch after batch without waiting for the user - intake new requests between batches, ship each batch through the full pipeline, and pause and resume on its own around session limits - until the backlog is done or something needs the user's decision. Use when the user says autoiterate, "keep iterating" or "work through the backlog". For a single cycle, use /iterate. | Optional: "stop", "stop now", N batches, or "until <ID>" |
| `audit` | Audit <Project>: run the tests, benchmarks, scans, usage report, change list and docs drift check, dispatch the always-on reviewers and those whose triggers fired (every reviewer for 'full') in parallel, triage their findings into BACKLOG.md, and summarise the top five items and the batches. Use when the user asks for an audit, a backlog refresh or "what should we improve next". | Optional: "full" for the milestone audit, or a focus, such as perf, security, efficiency or usage |
| `devmanual` | Print <Project>'s developers' guide, sized to the project - the framework size and its life cycle, the commands that apply, the current state and the upgrade trigger. 'full' prints the whole of docs/DEV_CYCLE.md. Use when the user asks how to work on the project, what the commands are, or what to do next. | Nothing for the short version, or "full" |

Standard and Full projects get all four; Light projects get none by default: `/devmanual` and at most one project skill, each when its trigger fires. The bodies follow Phase 12 step by step, including intake, the reviews-by-category table (Phase 11), the version-control flow for the profile, the limit rule and the report format. `/autoiterate` points at `/iterate`'s steps rather than copying them, so the two can't drift. Don't pin an effort level in any of these skills.

---

## Appendix G: Hook templates

The hook scripts live in the repo, in the folder the platform expects (Appendix I), and are wired to its hook events in the project settings. Appendix I has the reference wiring, including the session's model, effort and compaction settings. Each script needs two things from the platform, marked *platform binding* in the code: how it receives the command or file path it's checking, and how it blocks the action or reports a failure.

| Script | Wire it to | Timeout |
|---|---|---|
| `before_publish.py` | Before a shell command | Generous: at least 2× the tests and benchmarks together (for example 900 s) |
| `protect_baseline.py` | Before a file edit | About 10 s |
| `protect_secrets.py` | Before a file edit or a shell command | About 10 s |
| `after_edit.py` | After a file edit | About 60 s |

`before_publish.py` (the core, written for Git; adapt the test and bench commands to the stack, drop the bench step for a project without benchmarks, and for another version-control tool, change the publish pattern and the repository lookup as Appendix K says):

```python
import json, os, re, subprocess, sys
from pathlib import Path
# A real `git push`, allowing global options (git -C dir push), but not `git stash push` or "push" in a message
PUSH = re.compile(r"\bgit(?:\s+(?:-C\s+\S+|-c\s+\S+|--?[\w-]+(?:=\S+)?))*\s+push\b")
GIT_C = re.compile(r"\bgit\s+(?:-c\s+\S+\s+)*-C\s+(\"[^\"]+\"|'[^']+'|\S+)")
CD = re.compile(r"^\s*(?:cd|pushd|Set-Location|sl)(?:\s+-(?:Path|LiteralPath))?\s+(\"[^\"]+\"|'[^']+'|\S+)\s*$", re.I)
BLOCK = 2  # platform binding: the exit code that blocks the action (Appendix I)

def read_hook_input():
    """Platform binding (Appendix I): the shell command about to run, and its working directory."""
    try: data = json.load(sys.stdin)
    except ValueError: return "", os.getcwd()
    return str((data.get("tool_input") or {}).get("command", "")), data.get("cwd") or os.getcwd()

def to_native(path):  # Git Bash /e/dir -> E:/dir on Windows
    path = path.strip("\"'")
    m = re.match(r"^/([a-zA-Z])(/.*)?$", path)
    if os.name == "nt" and m: path = m.group(1).upper() + ":" + (m.group(2) or "/")
    return os.path.expanduser(path)

def repo_root(d):
    try: out = subprocess.run(["git", "-C", d, "rev-parse", "--show-toplevel"], capture_output=True, text=True, timeout=10)
    except (OSError, subprocess.SubprocessError): return None
    return Path(out.stdout.strip()).resolve() if out.returncode == 0 and out.stdout.strip() else None

ROOT = repo_root(str(Path(__file__).resolve().parent))  # the repo this hook lives in

def pushes_this_repo(command, cwd):
    """True if any push targets this repo, or its target can't be determined (fail safe)."""
    here = cwd
    for seg in re.split(r"&&|\|\||;|\n", command):
        if (cd := CD.match(seg)): here = str(Path(here, to_native(cd.group(1)))); continue
        if PUSH.search(seg):
            c = GIT_C.search(seg)
            target = str(Path(here, to_native(c.group(1)))) if c else here
            root = repo_root(target) if Path(target).is_dir() else None
            if root is None or root == ROOT: return True
    return False

def run(args):
    p = subprocess.run([sys.executable, *args], cwd=ROOT, capture_output=True, text=True, encoding="utf-8", errors="replace")
    return p.returncode, p.stdout + p.stderr

def main():
    command, cwd = read_hook_input()
    if not PUSH.search(command) or not pushes_this_repo(command, cwd): return 0
    if ROOT is None: sys.stderr.write("Publish blocked: can't find this hook's repository.\n"); return BLOCK
    code, out = run([str(ROOT / "tests" / "run_tests.py")])
    if code: sys.stderr.write("Publish blocked: tests failed.\n" + out[-2000:]); return BLOCK
    code, out = run([str(ROOT / "bench" / "run_bench.py"), "--compare"])
    if code: sys.stderr.write("Publish blocked: benchmark regression or broken budget.\n" + out[-2500:]); return BLOCK
    return 0

sys.exit(main())
```

Test the targeting logic from a file, not from a command line that contains the push text itself (that would fire the gate). Cover: a plain push, `cd <this repo> &&`, `git -C <this repo>`, `cd <other repo> &&`, Windows and Git Bash paths, and an unknown directory, which must be gated.

`protect_baseline.py`: deny an edit to `bench/baseline.json`, in the platform's deny format (Appendix I), with the reason "bench/baseline.json only changes through the --baseline command, and only when the numbers genuinely improved."

`after_edit.py`: if the edited file's path (from the hook's input) ends with a watched path, run the formatter and linter on that file (if the project has them), then the tests with `--quick`, and on failure report the tail of the output to the agent as an error it must fix (Appendix I).

`protect_secrets.py`: deny, as `protect_baseline.py` does, an edit whose new content matches a key pattern or a high-entropy token, and a command that reads `.env` or a credential file.

For Team and Enterprise, add a CI workflow (for example GitHub Actions, or a CI server watching the repository) that runs the same test and bench-compare commands on every PR, and make them required checks.

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
22. **Keep the always-loaded instructions an index.** The reference project's `CLAUDE.md` was 101 lines, well inside the usual guidance of 200, but 18 KB, because each line was a paragraph. About 5.5 KB of it was the model-and-effort section: dated reasoning and the story of a usage review, which no task needed but every session, and every agent told to read the file first, paid for on each turn. Budget the file in bytes as well as lines, keep rules and numbers in it with their reasons in the Decisions log, and point to docs, skills and path-scoped instructions for the rest.

---

## Appendix I: Platform bindings

The phases and templates describe the method in platform-neutral terms. This appendix binds each term to a concrete platform. To launch on a platform with no binding here, map each term to its nearest equivalent, note anything with no equivalent and how you worked around it, and record the mapping in the Decisions log. When editing Coldstarter, platform-specific detail goes here, not in the phases.

### I.1 Claude Code (the reference binding, checked 2026-09)

Docs: code.claude.com/docs (subagents, skills, hooks, settings, MCP).

| Neutral term | What it is | Claude Code |
|---|---|---|
| The agent | The AI coding agent running the launch | Claude Code, in the terminal, desktop app, web app or an IDE extension |
| Project instructions file | Loaded into every session; kept as an index (Phase 6) | `CLAUDE.md` at the repo root (or `.claude/CLAUDE.md`). Claude Code's own guidance is under 200 lines; Coldstarter's budget (Phase 6) is tighter. It reads `AGENTS.md` instead when there's no `CLAUDE.md`. To share one file with other tools, keep the instructions in `AGENTS.md` and put `@AGENTS.md` at the top of `CLAUDE.md`. `@path` imports load at session start, so they don't shrink the context. Block-level HTML comments are stripped before loading, so notes for people cost nothing. |
| Path-scoped instructions | Rules that load only when files in one area are read | `.claude/rules/*.md` with a `paths:` list of globs in the frontmatter, or a `CLAUDE.md` in a subdirectory, which loads when files there are read |
| Skill | A procedure loaded only when invoked or relevant | `.claude/skills/<name>/SKILL.md`, invoked as `/<name>` (I.4) |
| Subagent | A separately prompted agent with its own tools, model and effort | `.claude/agents/<name>.md`, settings in frontmatter (I.3) |
| Hook | A deterministic script run at a fixed point, such as before a command or after an edit | Scripts in `.claude/hooks/`, wired under `hooks` in the project settings (I.2, I.5) |
| Project settings | Checked-in settings for the session, permissions and hooks | `.claude/settings.json` (personal overrides in `.claude/settings.local.json`) |
| Model tiers | Strong, standard, light | Opus, Sonnet, Haiku (check the current models and prices) |
| Effort | How long the model reasons per turn | `effortLevel` in settings, `/effort` in a session, `effort` in agent frontmatter. Levels: low, medium, high, xhigh, max; Coldstarter's "top effort levels" are xhigh and max. |
| Context limit for the main session | Where a long session compacts | `autoCompactWindow` in settings |
| Clearing the session's context | Starting fresh between batches | `/clear` |
| Turn cap | A runaway guard on an agent | `maxTurns` in agent frontmatter |
| Cache lifetime | How long an agent's prompt cache survives idle time | Five minutes by default; `experimental: cacheTtl: 1h` in agent frontmatter for one hour |
| Resuming an agent | Continuing a finished or paused agent with its context | `SendMessage` to the agent's ID or name |
| Fork | An agent started with a copy of the session's context | The Agent tool's `fork` type |
| Change review | An automated review of a diff, at a named depth | `/code-review <level>`. Always name the level: with none, it reuses the level last typed. `high` and above fan out to several subagents. With a version-control tool other than Git, check it supports it; if not, have a reviewer agent review the diff. |
| Checkpoints | Undoing the agent's own edits within a session | Checkpointing: `/rewind` (or Esc twice) restores files and the conversation to an earlier prompt. It tracks the agent's file edits, not changes made by shell commands, and it's no substitute for version control. |
| Isolated working copies | Separate copies for parallel agents | Agent worktree isolation uses Git worktrees; with another tool, give each agent its own checkout. |
| Loop or scheduler | Re-runs a skill unattended, with wake-ups | `/loop /autoiterate`. A scheduled wake-up is at most an hour ahead, so a longer wait chains wake-ups. |
| Browser tool | Lets agents drive the running product | The Playwright MCP server (or Chrome DevTools MCP), in `.mcp.json`; its tools appear as `mcp__playwright__*` |
| Tool names | Read, search, shell, edit | `Read`; `Glob` and `Grep`; `Bash` and `PowerShell`; `Edit`, `Write` and `MultiEdit` |
| Usage plans | For the usage-profile suggestion (Phase 1) | Pro → Lean; Max or Team → Balanced; Enterprise, or API use where time matters more than tokens → Throughput |
| Usage data | Per-session token records for the usage report | Transcripts under `~/.claude/projects/<project path>/` (I.6) |
| Personal memory | Preferences that aren't project rules | Auto memory, in `~/.claude/projects/<project path>/memory/` |

### I.2 Project settings

The reference routing (Phase 13) and the hooks (Phase 14, Appendix G) in `.claude/settings.json`. Merge with the existing keys:

```json
{
  "model": "sonnet",
  "effortLevel": "medium",
  "autoCompactWindow": "200k",
  "hooks": {
    "PreToolUse": [
      { "matcher": "Bash|PowerShell",
        "hooks": [{ "type": "command", "command": "python \"$CLAUDE_PROJECT_DIR/.claude/hooks/before_publish.py\"", "timeout": 900 }] },
      { "matcher": "Edit|Write|MultiEdit",
        "hooks": [{ "type": "command", "command": "python \"$CLAUDE_PROJECT_DIR/.claude/hooks/protect_baseline.py\"", "timeout": 10 }] },
      { "matcher": "Edit|Write|MultiEdit|Bash|PowerShell",
        "hooks": [{ "type": "command", "command": "python \"$CLAUDE_PROJECT_DIR/.claude/hooks/protect_secrets.py\"", "timeout": 10 }] }
    ],
    "PostToolUse": [
      { "matcher": "Edit|Write|MultiEdit",
        "hooks": [{ "type": "command", "command": "python \"$CLAUDE_PROJECT_DIR/.claude/hooks/after_edit.py\"", "timeout": 60 }] }
    ]
  }
}
```

Enterprise (Phase 9): add `permissions` rules that allow only the approved tools and MCP servers.

### I.3 Agent settings

Each agent is a markdown file in `.claude/agents/`: the settings from its Appendix C template as YAML frontmatter, then the instructions. Name the model explicitly (`opus`, not `inherit`), so a strong-tier agent stays on Opus in a Sonnet session. A reviewer:

```markdown
---
name: <role>
description: <from the template>
model: sonnet            # opus for strong-tier roles
effort: medium
maxTurns: 60
tools: Bash, Read, Glob, Grep   # + mcp__playwright for UI roles; never Edit/Write
---

<the template's instructions>
```

An implementer adds the edit tools and the cache lifetime:

```yaml
name: implementer          # implementer-hard, implementer-deep
model: sonnet              # opus for implementer-hard and implementer-deep
effort: medium             # high for implementer-deep
maxTurns: 100              # 150 for implementer-hard, 200 for implementer-deep
tools: Bash, Read, Edit, Write, Glob, Grep
experimental:
  cacheTtl: 1h
```

If the project's agents aren't available as agent types in a session, run `general-purpose` agents told to follow the matching agent file (Phase 12).

### I.4 Skill settings

Each skill is `.claude/skills/<name>/SKILL.md`: frontmatter from its Appendix F row, then the body.

```markdown
---
name: iterate
description: <from Appendix F>
argument-hint: "[batch ID, item ID, 'ID solo', or nothing]"
---
```

Don't set `effort` in skill frontmatter, so the session's `/effort` still applies.

### I.5 Hook input and output

- **Input:** a hook receives JSON on stdin. `tool_input.command` holds a shell command, `tool_input.file_path` the file being edited, and `cwd` the working directory. `$CLAUDE_PROJECT_DIR` points at the project root.
- **Blocking:** a `PreToolUse` hook that exits 2 blocks the tool call, and its stderr goes to Claude. A `PostToolUse` hook that exits 2 reports its stderr to Claude as an error to fix (the edit has already happened).
- **Denying with a reason** (`protect_baseline.py`, `protect_secrets.py`): print this and exit 0:

  ```json
  {"hookSpecificOutput": {"hookEventName": "PreToolUse", "permissionDecision": "deny",
    "permissionDecisionReason": "<the reason>"}}
  ```
- **Timeouts:** a hook that times out is a non-blocking error, so the tool call goes ahead. Give gate hooks a generous `timeout` (Phase 14).

### I.6 Usage records

- **Where:** `~/.claude/projects/<the project path, with every non-alphanumeric character replaced by '-'>/`: one `.jsonl` transcript per main session, and `<session>/subagents/*.jsonl` per agent run, each with a `.meta.json` naming the agent's type.
- **What:** each assistant message carries its model and a usage block (input, cache writes split by five-minute and one-hour lifetime, cache reads, output). A message can appear more than once, so deduplicate by message id.
- **History:** `tools/usage_report.py --save` appends to `.claude/usage-history.json`.
- **Pricing note:** at 2026-09 prices, a cached read costs the same per token on Sonnet and Opus; Opus is dearer only for new input and output. Check current prices.

### Other platforms

`AGENTS.md` is a cross-tool convention for the project instructions file that many coding agents read; prefer it where the platform supports it. Add a section like I.1 to I.6 for each platform once a project has been launched on it, with the date its binding was checked.

---

## Appendix J: `docs/DESIGN.md` template

For anything with a user interface. Write it in Phase 4 from the visual direction agreed in Phase 2, before the first screen is built. Implementers read it before UI work, and the UX reviewer checks against it. If the organisation has a design system, this file points to it and records only the project's additions.

```markdown
# Design

The look, feel and interaction rules for <Project>. Read before any UI work.

## Direction
- For: <who, where, on what device, with how much time>
- Feels: <three words>
- Like: <two or three references, and what to take from each>
- Never looks like: <for example a generic SaaS landing page, a crypto dashboard>
- Density: <compact and scannable / generous>, because <reason>
- Serves the pillars: <from the project instructions file>

## Tokens: the only source of values
- They live in <file>: colour, type scale, spacing scale, radii, shadows, motion durations and easing.
- No raw values in components. A new value becomes a token first, with a reason.
- Before adding anything, reuse what the tokens and existing components already provide.

## Colour
- Colour has a job: state (success, warning, error, information), hierarchy, selection, or data. Filling space isn't a job.
- A neutral base with real contrast, one accent used sparingly, and state colours that don't clash with the accent.
- Text meets WCAG 2.2 AA contrast (4.5:1 for body text, 3:1 for large text and controls) in every theme. Nothing relies on colour alone.
- A dark theme is designed, not inverted: dark greys rather than pure black, and softened accents.

## Type
- One or two families, chosen for this product with a reason, not the platform default by habit.
- One type scale. Hierarchy comes from size and weight, not colour alone; headings and body text differ clearly.
- Reading text runs about 45 to 75 characters a line. Numbers that line up use tabular figures.

## Layout
- Structure comes from alignment, spacing and dividing rules before boxes. A card is for something the user acts on as a unit.
- The layout follows the content, not a template. Asymmetric layouts, split screens and type-led pages are all fair game.
- It works at the smallest supported width without sideways scrolling. Touch targets are at least 24 by 24 px (WCAG 2.2 AA), and 44 by 44 px for primary controls.

## States
Every view designs its empty, loading, error, partial and overflowing states (long names, huge numbers, zero items, a thousand items).

## Motion
Motion explains a change: where something came from or went. It's short (about 100 to 300 ms), never makes the user wait, and follows the reduced-motion setting.

## Copy
Plain, specific words in the users' own language. Say what a control does ("Export as CSV"), not how it should feel. No filler, and no placeholder text in anything that ships.

## Generic defaults to avoid
These are the most statistically likely choices, the ones that make an interface look machine-made. Each is allowed only when the direction above calls for it, with the reason written here.
- purple, violet or blue-to-purple gradients; neon glows on dark navy
- the stock landing page: a centred hero, three icon cards, a call to action, repeat
- every piece of content in its own rounded, shadowed card
- emoji or stock icons as decoration, such as an icon beside every heading
- glassmorphism, blurred blobs, gradient text and animated gradients for their own sake
- one corner radius, one shadow and one grey applied to everything
- everything centred
- buzzword copy: "seamless", "revolutionise", "empower", "unlock", "supercharge", "next-level"
- invented data that looks real in shipped screens: metrics, testimonials, customer logos

## Checks
- UI changes are checked by screenshot, at the sizes the project supports, against this file.
- <Automated where the stack allows: contrast and accessibility checks in the tests; a lint rule against raw colour values outside the tokens file.>
```

---

## Appendix K: Version control bindings

The phases describe version control in neutral terms, and this appendix binds them. Phase 1 chooses one of four: **Git** (the default), another tool the organisation uses, **snapshots**, or **none**. Git is the reference binding. For a tool not listed here (Mercurial, Perforce, Fossil and others), map each term to its nearest equivalent, note anything with no equivalent and how you worked around it, and record the mapping in the Decisions log.

- **Snapshots** keep a dated copy of the project folder, in a folder outside it, at each point where a tool would commit. No tool is needed, and a bad change can be undone by restoring a whole copy.
- **None** keeps no history at all. Edits go straight into the project folder. The agent's own checkpoints may undo changes within a session (Appendix I), but once the session ends, a change can only be undone by hand.

**When each fits.** Standard and Full projects need a version-control tool: the gates, rollback, isolated working copies and reviews all depend on it. A None-sized deliverable can use snapshots or nothing at all. A Light project can too, but only with the reason in the Decisions log, because it will keep changing and each change is a chance to break something that can't then be undone. Whatever the choice, secrets never go into the project folder or anything delivered.

**Check before setting up.** A tool isn't always installed. Git, for example, isn't on Windows by default. Run the tool's version check first, and if it's missing, give the user the install command for their system and ask before installing anything.

| Neutral term | What it means | Git (default) | Subversion | Snapshots | None |
|---|---|---|---|---|---|
| Install check | Is the tool there? | `git --version`. To install: Windows `winget install --id Git.Git -e`; macOS `xcode-select --install` (Apple's Command Line Tools); Linux the package manager (`sudo apt install git`) | `svn --version`. To install: Windows SlikSVN, or TortoiseSVN with its command-line tools; macOS `brew install subversion`; Linux `sudo apt install subversion` | — | — |
| Repository | Where the history lives | `git init` in the project folder. A remote on a host (GitHub, GitLab, Bitbucket, Azure DevOps, an organisation server) is optional. | A central repository on a server, or a local one: `svnadmin create <path>`, then `svn mkdir` its `trunk`, `branches` and `tags` folders, then `svn checkout file:///<path>/trunk` into the project folder | A snapshot folder outside the project folder | — |
| Author identity | Who the commits belong to | `git config user.name` and `user.email` in the repository's config only | The repository username (the server account, or `--username`). The name and email go in the file headers. | The file headers | The file headers |
| Ignore list | Files that never enter history | `.gitignore` | The `svn:global-ignores` property on the working copy's root (`svn propset`) | Leave outputs, caches and secrets out of each copy | — |
| Commit | Record a change in the history | `git commit` (local until published) | `svn commit` (goes straight to the repository, so it also publishes) | A dated copy of the project folder, named `<date>-<phase or item>` | Nothing; the phase ends when its work is saved |
| Publish | Share commits with the repository everyone uses | `git push`. With no remote there's nothing to publish: the gate's checks run before deploying, as for snapshots. | Part of `svn commit` | Deploying or delivering | Deploying or delivering |
| Publish gate | The hook that runs the tests and benchmarks first (Phase 14) | Hooks `git push` (Appendix G's script) | Hooks `svn commit` and `svn ci`: match `\bsvn\s+(?:commit\|ci)\b` instead of the push pattern, and find the target's root with `svn info --show-item wc-root`. Team and Enterprise add a server-side pre-commit hook. | The ship routine runs the checks before deploying; a hook gates the deploy command if there is one | As snapshots |
| Main line | The shared line of work | `main` | `trunk` (`^/trunk`) | — | — |
| Branch | A line of work kept apart until it's ready | `git switch -c batch/<id>-<slug>` | `svn copy ^/trunk ^/branches/batch-<id>-<slug>`, then `svn switch` to it | — | — |
| PR | A review request: a change proposed for review before it joins the main line | A pull request (GitHub, Bitbucket, Azure DevOps) or merge request (GitLab) | A review in the team's review tool, from a branch or a patch; merged with `svn merge` | — | — |
| Protected main line | Nothing joins it without CI and review | Branch protection rules and `CODEOWNERS` on the host | Path-based authorisation on the server, and a pre-commit hook | — | — |
| Signed commits | Cryptographic proof of the author | Supported (GPG or SSH signing) | Not supported: if policy requires them, choose Git | — | — |
| Set a change aside | Put a change away and bring it back (the benchmark A/B test, Phase 8) | `git stash`, then `git stash pop` | `svn diff > ../change.patch` and `svn revert -R .`, then `svn patch ../change.patch` | Copy the changed files out of the folder, and copy them back | As snapshots |
| Isolated working copies | Separate copies for parallel implementers and A/B arms | `git worktree add` | A second `svn checkout` | A copy of the folder; run one implementer at a time | Run one implementer at a time; no A/B experiments |
| Working copy status | What's changed and not yet committed | `git status` | `svn status` | The progress file and the latest snapshot | The progress file |
| Undo a shipped change | Reverse it in the history | `git revert <commit>` | `svn merge -c -<revision> .`, then commit | Restore a snapshot | The agent's checkpoints within the session; otherwise by hand |
| Commit reference | How the Done table and reports cite a change | The short hash | The revision number (`r123`) | The snapshot's name | The date |
