# Coldstarter

Copyright (c) 2026 Luke Bennie <lukebennie@gmail.com>. Licensed under CC BY-NC 4.0 (see `LICENSE`).

This repository holds **Coldstarter**, Luke Bennie's specification for Claude Code that launches any project, of any scale and in any domain: problem analysis, a first working version, and a full self-improving development framework. There's no code here. The product is the document, `COLDSTARTER.md`.

## Files

- `COLDSTARTER.md`: the specification. This is the product.
- `README.md`: what Coldstarter is and how to use it.
- `LICENSE`: CC BY-NC 4.0 (summary plus the full legal code).

## Editing the spec

- **Author and ownership:** Luke Bennie is the author. Keep the copyright line, the authorship table and the origin note at the top of `COLDSTARTER.md`. Commits are authored as Luke Bennie <lukebennie@gmail.com> (set in this repo's git config).
- **Versioning:** every change to `COLDSTARTER.md` bumps the version in its authorship table and adds a row to its version history:
  - patch (1.0.1): wording and fixes
  - minor (1.1): new sections, agents, rules or project types
  - major (2.0): changes to the phases or the loop's structure

  Update `README.md`'s Status line to match.
- **Keep it general:** Coldstarter must work for any project type and every scale profile. Game- or project-specific detail belongs only in clearly marked examples, and in the "Lessons from the reference build" appendix.
- **Keep it consistent:** a rule that appears in several places must match everywhere. That includes the ground rules, the scale-profile table, the phases, the agent roster, the batch categories, the triage appendix, the skill descriptions and the definition of done. After an edit, search the file for every place the changed concept appears.
- **Lessons come from real use:** when a project launched with Coldstarter teaches something, add it to Appendix H with the evidence (what happened and what it cost), and change the relevant phase or template so the lesson takes effect.
- **Style:** plain, direct sentences, written for a person who starts cold. Tables for reference material. No filler.

## Git

- Branch `main`. The repo is public on GitHub (`lbennietech/coldstarter`). Each version bump gets a matching GitHub release tagged `v<version>`.
- Ask before changing the repo's visibility, the licence, or pushing anywhere new.
