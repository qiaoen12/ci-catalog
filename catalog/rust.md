# Rust / Tauri / Docker CI recipe notes

## Rust

Prefer Cargo / rustup-native commands:

```text
cargo fmt --all --check
cargo clippy ...
cargo test ...
cargo build / cargo check ...
```

Do not wrap each Cargo command in a custom Action without a concrete benefit.

## Tauri

Treat ordinary PR CI and release automation separately.

Fast PR CI may combine:

```text
Node/TypeScript checks
+
Rust fmt/clippy/test/check
```

Full Windows/macOS/Linux application packaging, installers, signing, and release publishing are Heavy / Release concerns unless real product risk proves they must be Required on every PR.

Use upstream Tauri Action for Tauri build/release plumbing when needed; do not use it as a replacement for normal lint/test commands.

## Docker

Docker builder and build/push plumbing are complex platform capabilities.

Prefer Docker upstream official Actions for Buildx/build-push rather than custom shell implementations.

## Reusable workflow status

DEFER.

Do not create one giant `tauri-basic.yml`. If stable reuse later appears, prefer capability boundaries such as Node, Rust, or Docker rather than product-name profiles.
