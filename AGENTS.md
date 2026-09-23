# ci-catalog agent-card

## G-lite v3.4 binding

- Developer: `g-lite-developer[bot]` / App ID `5017695`.
- Reviewer: `g-lite-reviewer[bot]` / App ID `5010632`.
- Human Authority: the human repository controller owns Genesis, governance, and the final Squash merge.
- Local Bootstrap configures identity only. GitHub is the SSOT; this repository keeps no local task, review, approval, or merge state.
- Current status: ACTIVE; consumer Required Check: `catalog-ci`.

## Start

- Read root `README.md`, this file, and the current GitHub Issue Contract; new tasks record Original Intent before the Contract.
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

## Consumer CI adoption

When the task is to initialize or update CI for a consumer repository:

- read and follow `ADOPTION.md`;
- inspect the consumer repository before selecting CI capabilities;
- report planned checks before modifying the consumer;
- preserve repository-specific checks unless the current task explicitly authorizes changing them;
- do not modify the consumer Ruleset automatically;
- do not add a reusable workflow without real cross-repository duplication evidence that satisfies the abstraction gate.

Keep the detailed adoption procedure in `ADOPTION.md`; do not create a parallel procedure here.

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

1. Read the current Issue Contract and Original Intent; verify OPEN and fresh independent authorization.
2. Create a branch/worktree from current `origin/main`; change only Contract scope.
3. Check the actual API Actor, commit author and Git transport for each critical action. Developer clone/fetch/push uses App HTTPS credentials; isolate global Git `insteadOf` rewrites and check effective remote and local config before transport. Never push or merge as Human Authority.
4. For local credential discovery check `.g-lite-local/credentials` first, then machine-local `~/.config/g-lite/` if absent or unusable. In each checkout, add `.g-lite-local/` to Git's local exclude before creating its credential symlink; do not commit or disclose credential material.
5. Run repository checks; push and open a PR with Why / What / Test / Unverified-Risks / `Fixes #N`.
6. Wait for `catalog-ci`. Stop: the independent Reviewer reviews and Human Authority alone merges.

## Main continuous delivery

Main coordinates CI and Review without becoming a fourth GitHub Actor or writing Developer's PR branch. Failed CI or REQUEST_CHANGES goes back to Developer for in-scope repair and a new HEAD; wait for `catalog-ci` and independent Reviewer re-review on that HEAD. Reviewer rechecks Issue authorization freshness, diff and current Checks before APPROVE or REQUEST_CHANGES; Reviewer must never push or merge.

Before final merge, Main reads live main B and PR HEAD H; use GitHub compare or `git merge-base --is-ancestor` to prove B is an ancestor of H. If not, only Developer updates/rebases the branch using App HTTPS and pushes new HEAD; rerun CI, independent Review and full preflight. Human Authority may explicitly preauthorize this task with `merge-authorized`: Main verifies a human API Actor created its latest label event no earlier than the Issue's last body edit and that it remains attached and unrevoked. Without fresh label, ask Human Authority for current-task merge confirmation. Check OPEN Issue, independent fresh `approved`, OPEN non-draft PR targeting main, B ancestor of H, current-HEAD `catalog-ci` PASS and independent Reviewer APPROVE, plus GitHub merge eligibility; recheck immediately before Human Authority squash merge locked to expected H (`gh pr merge --squash --match-head-commit H`). Read GitHub merged state, merge SHA and Issue state afterward. The Ruleset is not strict latest-base, so main may advance in the final comparison-to-merge window.

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
