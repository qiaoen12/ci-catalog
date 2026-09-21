# CI adoption protocol

Use this protocol when an Agent is asked to initialize or update CI for a consumer repository by using `qiaoen12/g-ci-catalog`.

This file is an adoption procedure, not an installer. Adapt CI to the repository; do not adapt the repository to CI.

## Standard tasks

### A. CI initialization

A user may use the short instruction:

> Read `qiaoen12/g-ci-catalog` and initialize CI for the current repository.

Use this when the consumer has no CI or does not yet have a clear minimum PR CI.

### B. CI update

A user may use the short instruction:

> Read `qiaoen12/g-ci-catalog` and update CI for the current repository.

Use this when the consumer already has CI and the task is to improve, repair, or align it without replacing valid repository-specific behavior.

## Required execution order

### 1. Inspect the consumer first

Before choosing any CI capability, inspect the current consumer repository and identify:

- technology stack;
- lockfile and package manager;
- existing GitHub Actions workflows;
- existing formatter, lint, typecheck, test, and build CLI entry points;
- repository-specific checks;
- current GitHub Required Check names, when readable;
- current task or Issue Contract boundaries, when the repository uses them.

Do not start by copying a catalog example or by guessing the stack.

### 2. Read ci-catalog

Read:

- `README.md`;
- `AGENTS.md`;
- `ADOPTION.md`;
- the `catalog/*.md` files relevant to the consumer's actual stack and requirements.

Use catalog guidance only after the consumer inventory is understood.

### 3. Choose the smallest suitable capability

Evaluate capability sources in this order:

```text
GitHub platform
→ GitHub official Action
→ upstream official Action
→ mature trusted third-party Action
→ project-native CLI
→ self-built implementation last
```

Prefer the simplest layer that solves the real requirement.

For formatter, lint, typecheck, test, and build checks, prefer stable project-native commands that the consumer already uses.

## Preservation rules

CI adoption must not, merely for CI convenience:

- change the package manager;
- replace the test framework;
- replace existing lint or typecheck tools;
- add unnecessary dependencies;
- create a complex reusable workflow automatically;
- scan and restructure the project automatically;
- modify the consumer GitHub Ruleset automatically;
- remove repository-specific checks automatically.

Preserve existing checks unless the current task explicitly authorizes changing or removing them.

Repository-specific checks stay in the consumer.

## Plan before modification

Before changing the consumer repository, report:

```text
Detected stack:
Checks retained:
Checks planned:
Action / CLI for each check:
Expected stable Required Check:
Explicit non-changes:
Unverified / Risks:
```

The plan must make clear what will run on pull requests and why.

Do not implement until the plan is clear. If a material ambiguity requires a human decision, stop at the plan instead of guessing.

## Default CI target

Unless the consumer's current task explicitly requires more, target a Fast PR CI:

- clear success / failure behavior;
- minimum GitHub token permissions;
- executed external Actions pinned to a full commit SHA;
- repository-specific checks kept local to the consumer;
- stable Required Check naming;
- Heavy, Release, publishing, deployment, or long-running checks kept out of the ordinary PR gate by default.

Do not add `pull_request_target` to execute untrusted pull request code.

A reusable workflow from `g-ci-catalog` may be used only when that reusable workflow already exists for a proven reuse case, and the consumer reference must use an exact commit SHA.

Do not create a new reusable workflow merely because several YAML lines look similar. The existing ci-catalog abstraction gate still applies.

## Validation after implementation

Before declaring the consumer CI complete, verify:

- workflow YAML is valid;
- a normal pull request path can PASS;
- at least one controlled, relevant failure can BLOCK;
- the intended Required Check name is stable;
- executed external Actions do not use floating references;
- no `pull_request_target` was introduced;
- existing repository-specific checks were not silently removed;
- the implementation remains inside the current task or Issue Contract scope.

If a validation step cannot be performed, report it as unverified instead of assuming success.

## Final report

Report at least:

```text
Changed files:
Checks added:
Checks retained:
Actions / CLI used:
Required Check:
Validation result:
Unverified / Risks:
Cross-repository reuse evidence:
```

For `Cross-repository reuse evidence`, state whether this real consumer exposed stable duplicated logic that may justify a future ci-catalog update.

Do not promote that observation into a reusable workflow during the consumer task unless a separately authorized ci-catalog task satisfies the abstraction gate.
