# Node / TypeScript CI recipe notes

## Default posture

Use the consumer's real package manager and project scripts.

Do not force npm, pnpm, yarn, ESLint, Biome, or one framework across all consumers.

## Typical shape

```text
setup Node
→ frozen/locked dependency install
→ lint
→ typecheck
→ test when real tests exist
→ build when the product is buildable
```

Examples:

- npm with a valid lockfile: prefer `npm ci` in CI.
- TypeScript: prefer the project script such as `npm run typecheck`.
- lint: prefer the project's existing script.
- build: prefer the project's existing script.

## Monorepo rule

The consumer declares its workspaces / working directories.

`ci-catalog` must not scan a repository and infer frontend/backend topology.

Split jobs when components have genuinely independent dependencies, failure meaning, or useful parallelism. Do not split merely because many directories exist.

## Reusable workflow status

DEFER.

Node/TypeScript is a strong future candidate because install → lint → typecheck → test → build often repeats, but the abstraction must be proven by real consumers first.
