# ci-catalog

Reusable CI capability catalog for personal and small-team GitHub projects.

`ci-catalog` does **not** implement a CI runtime. It records reviewed CI capabilities, safe defaults, and—only after real duplication is proven—small reusable GitHub Actions workflows.

## Agent quick start

For CI initialization:

> Read `qiaoen12/ci-catalog` and initialize CI for the current repository.

For CI update:

> Read `qiaoen12/ci-catalog` and update CI for the current repository.

Then follow `ADOPTION.md` before making changes.

## Mission

Use existing platform and ecosystem capabilities before writing new CI machinery.

Default source order:

```text
GitHub platform
→ GitHub official Action
→ tool / stack upstream official Action
→ mature trusted third-party Action
→ project-native CLI
→ self-built implementation (last resort)
```

The catalog standardizes **what must be proven**, not one mandatory tool for every project.

## Current phase

Bootstrap / inventory-first.

At this stage:

- maintain reviewed capability notes;
- use full commit SHA for external Actions that are actually executed here;
- let real consumer repositories assemble their own CI first;
- do not create `python-basic.yml`, `node-basic.yml`, `rust-basic.yml`, etc. until stable cross-repository duplication is demonstrated.

A few copied lines of stable YAML are not, by themselves, enough reason to create a reusable workflow.

## Ownership model

```text
GitHub / upstream projects
→ provide Actions and platform capabilities

ci-catalog
→ reviews, records and, when justified, combines stable common capabilities

consumer repository
→ chooses what it needs, runs project-native commands, keeps repo-specific checks

GitHub Ruleset
→ enforces the consumer's stable Required Check
```

`ci-catalog` must not:

- auto-detect consumer technology stacks;
- inject workflows into consumers;
- modify consumer Rulesets;
- manage Issues, Agents, deployments, or business state;
- absorb repository-specific checks;
- replace project-native lint/test/build commands;
- grow profiles for hypothetical future needs.

## Abstraction rule

Promote executable logic into a reusable workflow only when there is real evidence that:

1. multiple real consumers repeat substantially the same logic;
2. the meaning is stable across those consumers;
3. centralized maintenance is cheaper than keeping the small YAML local.

Otherwise keep the knowledge in `catalog/*.md`.

## G-lite-compatible development protocol

This repository adopts the G-lite GitHub-native collaboration protocol:

```text
Human
→ Issue Contract
→ fresh independent `approved`
→ Developer branch / worktree
→ PR
→ Required Check `catalog-ci`
→ independent Reviewer
→ squash merge
```

GitHub is the source of truth for authorization, PR state, checks, reviews and merge result.

No G-lite CLI, Router, Controller, Worker, reviewer runtime, or local task/review/merge state is required.

## Repository layout

```text
README.md
AGENTS.md
ADOPTION.md
catalog/
  actions.md
  python.md
  node.md
  rust.md
  security.md
.github/
  ISSUE_TEMPLATE/task.md
  pull_request_template.md
  dependabot.yml
  workflows/catalog-ci.yml
```

## Consumer rule

Consumers keep their own `.github/workflows/ci.yml`.

A consumer may call a reusable workflow from this repository only after that workflow exists for a proven reuse case. Consumer references must use an exact commit SHA, never `@main`.

Repository-specific checks stay in the consumer.
