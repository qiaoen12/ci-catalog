# Reviewed capability inventory

This file records candidate building blocks. Listing an Action here does not mean every consumer should use it.

## Status vocabulary

| Status | Meaning |
| --- | --- |
| ADOPT | Preferred when the capability is actually needed |
| CLI-FIRST | Prefer the project's existing CLI |
| OPTIONAL | Useful only for a demonstrated case |
| DEFER | Do not add until a real requirement exists |
| REJECT | Do not use as the default approach |

## Selection rule

```text
GitHub platform
→ GitHub official
→ upstream official
→ mature trusted third-party
→ project-native CLI
→ self-build last
```

For executable external Actions, resolve and pin an exact commit SHA at adoption time.

## Current inventory

| Capability | Preferred source | Status | Notes |
| --- | --- | --- | --- |
| Checkout repository | `actions/checkout` | ADOPT | GitHub official. Pin full SHA. |
| Python runtime | `actions/setup-python` | ADOPT | Use for Python consumers; do not reimplement environment setup. |
| Node runtime | `actions/setup-node` | ADOPT | Use for Node consumers; built-in package-manager caching may be enough. |
| Dependency cache | setup Action built-ins first | OPTIONAL | Performance optimization, not correctness. |
| Artifacts | GitHub official artifact Actions | ADOPT WHEN NEEDED | Only when a workflow really produces artifacts. |
| Dependency review | GitHub-native / official Action | OPTIONAL | Add when risk and plan support justify it. |
| Code scanning | GitHub CodeQL | OPTIONAL | Prefer platform capability; risk-based, not universal Day-1 requirement. |
| Docker Buildx | Docker upstream official Action | ADOPT WHEN NEEDED | Prefer upstream Action over custom shell plumbing. |
| Tauri release/build automation | Tauri upstream Action | HEAVY / RELEASE | Not a replacement for ordinary Rust/Node lint and tests. |
| Ruff | project CLI first | CLI-FIRST | Official Action is optional, not mandatory. |
| pytest / unittest | project CLI | CLI-FIRST | Catalog must not force one testing framework. |
| TypeScript typecheck | project script / `tsc` | CLI-FIRST | Prefer `npm run typecheck` or existing project script. |
| Rust fmt/clippy/test/build | Cargo CLI | CLI-FIRST | Stable native toolchain commands. |

## Executed in this repository

`catalog-ci.yml` currently executes:

- `actions/checkout` pinned to commit `3d3c42e5aac5ba805825da76410c181273ba90b1` (`v7.0.1` at bootstrap time).

Do not mechanically update this table from tags. Upgrades should arrive as reviewed PRs.
