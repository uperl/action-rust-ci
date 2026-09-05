# action-rust-ci

A reusable GitHub Actions workflow for standard Rust CI: `cargo fmt --all --check`,
`cargo clippy`, and `cargo test`, each as a separate parallel job.

The toolchain is installed with [`dtolnay/rust-toolchain`](https://github.com/dtolnay/rust-toolchain),
and the `clippy` and `test` jobs share a build cache via
[`Swatinem/rust-cache`](https://github.com/Swatinem/rust-cache).

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
    uses: uperl/action-rust-ci/.github/workflows/ci.yml@main
```

Pin to `@v1` instead of `@main` for stability; `v1` tracks the latest 1.x release.

## Inputs

All inputs are optional.

| Input          | Type    | Default                                          | Description                              |
|----------------|---------|---------------------------------------------------|-------------------------------------------|
| `runs-on`      | string  | `ubuntu-latest`                                    | Runner label used for all jobs            |
| `toolchain`    | string  | `stable`                                           | Rust toolchain channel (`stable`, `beta`, `1.75.0`, ...) |
| `run-fmt`      | boolean | `true`                                             | Run the `cargo fmt --all --check` job     |
| `run-clippy`   | boolean | `true`                                             | Run the `cargo clippy` job                |
| `run-test`     | boolean | `true`                                             | Run the `cargo test` job                  |
| `clippy-flags` | string  | `--all-targets --all-features -- -D warnings`      | Flags passed to `cargo clippy`            |
| `test-flags`   | string  | `--all-features`                                   | Flags passed to `cargo test`              |

## Example: customizing

```yaml
jobs:
  ci:
    uses: uperl/action-rust-ci/.github/workflows/ci.yml@main
    with:
      toolchain: beta
      run-fmt: false
      test-flags: --all-features --workspace
```

## Notes

- `CARGO_TERM_COLOR` is set to `always` so cargo output stays colored in the Actions log.
- `clippy-flags` and `test-flags` are interpolated directly into the `cargo` command line,
  so everything after `--` in `clippy-flags` is passed through to the lint driver.
- The workflow only defines `workflow_call`; trigger it from a caller workflow with its
  own `on:` block (see [Usage](#usage)).
