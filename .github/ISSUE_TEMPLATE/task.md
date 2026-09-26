---
name: Task
about: Define a small, reviewable ci-catalog task contract
title: ""
labels: []
assignees: []
---

## Original Intent

<!-- Preserve the original request in the requester's words before scoping the Contract. -->

## Contract

### Goal

<!-- What concrete ci-catalog outcome is required? -->

### Acceptance

<!-- Observable conditions that must be true when complete. -->

### Out of scope

<!-- What must not be changed or added in this task. -->

### Evidence for abstraction

<!-- If adding executable reusable CI logic: which real consumers repeat it, and why is catalog abstraction justified? Write N/A for inventory/documentation tasks. -->

### Authorization

The current Contract must be reviewed by a human.

A GitHub Actor that wrote or materially edited this Contract must not add `approved` to the same Contract version. The normal independent approval actor is `g-lite-reviewer[bot]`; Developer and Reviewer remain separate.

Chat instructions are not authorization. Development starts only after an independent Actor adds `approved`.

<!-- g-lite:managed protocol start -->
### G-lite delivery lifecycle

For medium-or-larger work, record an Implementation & Verification Plan in the Contract.

- Keep the primary checkout on default/main; make task edits in one dedicated native Git worktree on one writable task branch.
- Reach LOCAL GREEN before opening the PR.
- REVIEW-READY requires the intended PR HEAD, CI GREEN for that current HEAD, and no unresolved Contract blocker.
- An independent Reviewer reviews the current Contract and PR HEAD.
- Human Authority performs the final Squash merge after required gates pass.
<!-- g-lite:managed protocol end -->
