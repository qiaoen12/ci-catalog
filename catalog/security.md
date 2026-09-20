# Security / supply-chain baseline

## Platform first

Prefer GitHub-native security capabilities before adding catalog code.

Baseline direction:

- Secret scanning when available;
- Push protection when available;
- minimal workflow permissions;
- default `permissions: contents: read`;
- full commit SHA pins for executed external Actions;
- full commit SHA pins for cross-repository reusable workflows;
- no `pull_request_target` for executing untrusted PR code;
- dependency review / CodeQL only when the repository's real risk and platform support justify them.

## What normal CI should not do

- Do not block ordinary PRs merely because dependencies are not the newest version.
- Do not add every scanner on Day 1.
- Do not duplicate GitHub security products with a custom `ci-catalog` security framework.
- Do not store secrets, PATs, host inventories, or credentials in this repository.

## Upgrade model

```text
pinned SHA
→ Dependabot proposes upgrade PR
→ catalog-ci
→ independent Review
→ squash merge
```

Automatic discovery of an update is allowed. Automatic silent adoption across consumers is not.
