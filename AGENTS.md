# ci-catalog agent-card

## Start

- Read root `README.md`, this file, and the current GitHub Issue Contract.
- Treat current GitHub facts as the source of truth.
- Do not infer authorization from chat.
- Confirm the Issue is OPEN.
- Before development, verify the current `approved` authorization is fresh and independent.
- If required GitHub facts cannot be read reliably, STOP.

## G-lite authorization freshness

Read:

- current Issue body;
- Issue `lastEditedAt`;
- latest `approved` label event;
- approved Actor;
- approved timestamp.

Decision:

```text
approved missing
→ INVALID

lastEditedAt == null
→ FRESH

lastEditedAt <= approvedAt
→ FRESH

lastEditedAt > approvedAt
→ STALE
```

The Actor that wrote or materially edited the current Contract version must not be the Actor that approves that same version.

INVALID / STALE / non-independent authorization means no development should start or continue under that authorization.

Do not create local approval caches, Contract hash state, or parallel GitHub state.

## ci-catalog source priority

For every proposed capability, evaluate in this order:

```text
1. GitHub platform-native capability
2. GitHub official Action
3. upstream official Action
4. mature trusted third-party Action
5. project-native CLI
6. self-built implementation
```

Prefer the simplest layer that solves the real requirement.

## Catalog-specific hard rules

- Do not create reusable workflows merely to remove a few repeated YAML lines.
- Do not create profiles for hypothetical future stacks.
- Do not auto-detect consumer stacks.
- Do not inject workflows into consumer repositories.
- Do not modify consumer Rulesets.
- Do not absorb repo-specific business checks.
- Do not invent replacement commands such as `ci-catalog test`.
- Prefer project-native CLI for formatter/lint/typecheck/test/build when the project already has a stable entry point.
- Prefer official/upstream Actions for complex platform capabilities such as runtime setup, artifact handling, Docker builders, release plumbing, and platform security.
- External Actions executed by this repository must be pinned to a full commit SHA.
- Reusable workflows consumed from another repository must be pinned to a full commit SHA.
- Do not use floating `@main` references.
- Do not use `pull_request_target` to execute untrusted PR code.
- Default workflow permissions to `contents: read`; add more only when a real task requires it.
- CI verifies; it should not silently rewrite consumer business code.

## Abstraction gate

Before adding an executable reusable workflow, document:

1. the real consumers;
2. the duplicated logic;
3. why local duplication is materially harder to maintain;
4. which parts remain consumer-specific;
5. failure behavior;
6. expected Required Check integration;
7. upgrade and SHA-pin behavior.

If this evidence does not exist, update `catalog/*.md` instead of creating a workflow.

## Developer delivery

1. Read the current Issue Contract.
2. Verify fresh independent authorization.
3. Create a branch/worktree from current `origin/main`.
4. Change only Contract scope.
5. Run repository checks.
6. Push and open a PR with Why / What / Test / Unverified-Risks / `Fixes #N`.
7. Wait for `catalog-ci`.
8. Stop. Review and merge belong to the independent Reviewer and GitHub.

## Reviewer protocol

The Reviewer must read:

- current Issue Contract;
- current authorization freshness;
- current PR HEAD;
- current diff;
- Required Checks.

Reviewer reports at least:

```text
Reviewed HEAD:
Contract last edited:
Approved at:
Approved by:
Fresh authorization:
Required Checks:
Verdict:
```

Developer Actor must not provide its own Required Review.

## Current development posture

Inventory first.

Do not add `python-basic.yml`, `node-basic.yml`, `rust-basic.yml`, `tauri-basic.yml`, or similar profiles until a real cross-repository reuse case proves the abstraction.
