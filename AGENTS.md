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
4. For Developer / Reviewer identity, invoke the configured role entry first and let it verify the API Actor. Inspect `.g-lite-local/credentials` or machine-local `~/.config/g-lite/` only when no role entry can be called, and only for existence, type, and accessibility. In each checkout, add `.g-lite-local/` to Git's local exclude before creating its credential symlink; do not commit or disclose credential material.
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

<!-- g-lite:managed protocol start -->
# Agent protocol

This repository uses the G-lite GitHub-native protocol.

## Contract

For new tasks, record Original Intent (the user's words or a fixed PRD reference) before the Issue Contract containing Goal, Acceptance, Out of scope, and Authorization. This version does not require reconciler to audit older consumers for Original Intent.

Before development and again before PR review, read the current OPEN Issue Contract, author/editor, lastEditedAt, current approved label and latest approved label event (actor and timestamp). Missing approval is INVALID; lastEditedAt absent or <= approvedAt is FRESH; later edits are STALE. Stop if facts cannot be verified or authorization is invalid/stale. The Actor that writes or materially edits the current Contract version cannot approve it. Reauthorization requires independent fresh approved; do not cache authorization.

## Roles

- Main coordinates the Issue → Developer → CI → independent Reviewer → rework → merge flow; it is not another GitHub Actor and must not write the Developer's PR branch. It may mechanically merge with Human Authority credentials only after that human explicitly authorizes the current task.
- Developer Actor: machine identity / GitHub App; may create/edit Contracts, develop, push, and open/update PRs within fresh authorized scope. It must not approve its own Contract, provide its own Required Review, or merge.
- Reviewer Actor: independent machine identity / GitHub App; may independently add approved and review the current PR HEAD with APPROVE / REQUEST_CHANGES. It must not develop, push, change repository governance, or merge.
- Developer and Reviewer must not independently change the Ruleset / governance that constrains them.
- Human Authority: one or more human accounts with appropriate permissions on this repository. After GitHub gates pass, it may perform final Squash merge through GitHub UI, CLI, API, or tools under its explicit instruction.
- Concrete account/App bindings are replaceable per consumer; no canonical username or App is required. Verify both Apps' identities, independence, and installation access externally. Do not add a Merge Bot / Merge Executor or identity registry.

## Genesis / ACTIVE

Human Authority controls Genesis: create the repository, install/authorize both Apps, establish initial protocol files and CI, configure Ruleset / governance / security, and verify ACTIVE readiness. Tools may execute under Human Authority's identity and authorization; this does not grant Developer administrator powers.

In ACTIVE, Developer + Reviewer handle daily tasks; Human Authority intervenes at governance boundaries or final merge.

## Local Bootstrap

Local Bootstrap ≠ Repository Task. Local App private key installation/rotation, ~/.config/g-lite/ credential directories, token helpers, shell identity bootstrap, read-only identity preflight, and new-machine identity setup need no Issue Contract. They do not authorize changing repository durable facts; repository changes enter the appropriate lifecycle.

In each checkout that uses the local credential entry, first add `.g-lite-local/` to that checkout's Git local exclude (locate it with `git rev-parse --git-path info/exclude`). Then create a `.g-lite-local/credentials` symlink to the actual machine credential root. Neither the symlink nor its target belongs in Git; do not change repository `.gitignore` or assume fixed Developer / Reviewer private-key file layouts. Verify the entry is ignored and a previously clean `git status` remains clean.

Developer / Reviewer use short-lived Installation Access Tokens. Never put private keys, JWTs, tokens, or PATs in repo, Issue, PR, evidence logs, or canonical state; do not persist tokens in state files. Local credentials stay in external secure mechanisms, outside canonical runtime.

For Developer / Reviewer operations, first invoke the configured role entry available in the current workspace / machine. The role entry must live-verify the expected API Actor and target repository access; credential paths, environment variables, or a human `gh` login do not establish identity. An Actor or access mismatch is `BLOCK`: stop the role action, do not guess private-key layouts or attempt temporary authentication, and do not fall back to Human identity.

Only when no callable role entry is available, inspect `.g-lite-local/credentials` and the machine-local `~/.config/g-lite/` entry for minimal existence, type, and accessibility metadata. Do not enumerate or display credential contents, or record private keys, JWTs, tokens, PATs, or resolved machine-specific credential absolute paths in the repository, Issue, PR, evidence logs, or canonical state.

Verify API Actor, commit author, and Git transport separately. Developer clone/fetch/push uses App HTTPS credentials. Before each operation verify the effective HTTPS remote and absence of applicable insteadOf rewrite: user/global Git config can silently turn HTTPS into human SSH authentication. Prefer task-process config/credential isolation, inspect repo-local config, and preserve existing user global Git / SSH settings.

Human, Developer, and Reviewer credentials may coexist on one Mac. Before each key GitHub / Git action verify the actual API Actor, transport, and role. Developer must not push/merge as Human Authority; Reviewer must not Review as Developer or Human Authority. Stop an action on identity mismatch. Physical credential isolation is future hardening, not a v3.4 Freeze condition.

## GitHub facts

GitHub is the source of truth for Issue authorization, PR, Checks, Review, Ruleset, merge eligibility, and merge result. Do not create local task, review, merge, or approval state or a second GitHub database.

The default branch requires PRs, at least one independent approval, stale review dismissal, a stable consumer-owned Required Check, squash-only merge, and no routine bypass. Enable Secret scanning / Push protection where supported. Consumer CI is owned by this repository and its Agent; G-lite does not generate or select it. Reconciler App assertions are invocation-only, require external verification of both roles, and do not authorize governance writes or manage credentials.

## Main delivery SOP

1. Main follows CI and Review. Failed CI or REQUEST_CHANGES returns to Developer for in-scope repair and a new HEAD.
   Wait for Required Checks on that HEAD and independent Reviewer re-review.
2. Before merge, read live main SHA B and PR HEAD H; prove B is an ancestor of H with GitHub compare
   or `git merge-base --is-ancestor`. On mismatch, Main does not update the PR branch.
   Developer uses App identity to update main/rebase/merge base and push a new HEAD;
   only Developer may run `gh pr update-branch` when it writes the branch. Repeat CI, Review, and preflight.
3. `merge-authorized` is an optional one-time Genesis prerequisite for automatic completion.
   On explicit task-level instruction from Human Authority, Main verifies that human API Actor and creates/applies the label if absent.
   Reconciler bootstrap does not create it. Without fresh label, ask Human Authority again before final merge;
   Developer-authored Issue text does not grant merge permission.
4. For automatic merge, verify the label is still attached, its latest LabeledEvent Actor is an authorized human,
   its createdAt is no earlier than Issue body lastEditedAt, and authorization has not been revoked.
5. Preflight live GitHub facts: OPEN Issue, current Contract with independent fresh approved,
   OPEN non-draft PR targeting main, B ancestor of H, Required Checks PASS and independent APPROVE on H,
   and merge eligibility. Re-read B, H, authorization, and gates immediately before merge;
   verify Human Authority API Actor, then squash merge with expected H (`gh pr merge --squash --match-head-commit H`).
   Read merged state, merge commit SHA, and Issue state; report Checks, Review, and unverified items.
   Without a strict latest-base Ruleset, main can advance between the last read and merge.
6. Ask Human Authority for scope changes, unverifiable identity/authorization/gates, governance or high-impact actions,
   or about three failures on one path without new evidence. Continue routine CI and Review rework within scope.
<!-- g-lite:managed protocol end -->
