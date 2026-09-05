# action-rust-ci

A reusable GitHub Actions workflow for standard Rust CI: `cargo fmt --check`,
`cargo clippy`, and `cargo test`, each as a separate parallel job.

## Usage

In the consuming repo, add `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  ci:
    uses: plicease/action-rust-ci/.github/workflows/ci.yml@main
```

Pin to a tag (e.g. `@v1`) instead of `@main` for stability once this repo has releases.

## Inputs

All inputs are optional.

| Input          | Type    | Default                                          | Description                              |
|----------------|---------|---------------------------------------------------|-------------------------------------------|
| `runs-on`      | string  | `ubuntu-latest`                                    | Runner label used for all jobs            |
| `toolchain`    | string  | `stable`                                           | Rust toolchain channel (`stable`, `beta`, `1.75.0`, ...) |
| `run-fmt`      | boolean | `true`                                             | Run the `cargo fmt --check` job           |
| `run-clippy`   | boolean | `true`                                             | Run the `cargo clippy` job                |
| `run-test`     | boolean | `true`                                             | Run the `cargo test` job                  |
| `clippy-flags` | string  | `--all-targets --all-features -- -D warnings`      | Flags passed to `cargo clippy`            |
| `test-flags`   | string  | `--all-features`                                   | Flags passed to `cargo test`              |

## Example: customizing

```yaml
jobs:
  ci:
    uses: plicease/action-rust-ci/.github/workflows/ci.yml@main
    with:
      toolchain: beta
      run-fmt: false
      test-flags: --all-features --workspace
```
