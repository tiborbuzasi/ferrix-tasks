# 001 Project Initialization

## Goal

Initialize the Ferrix Tasks project with complete development environment and CI pipeline.

## Scope

- Create Rust project with standard layout
- Add required dependencies (Floem, ezcal, dirs, tracing, thiserror, anyhow)
- Configure Rust toolchain (current stable)
- Configure code formatters and linters
- Create containerized dev environment (devcontainer)
- Create CI pipeline
- Set up scenario-linked test infrastructure

## Non-Goals

- No pre-commit hooks
- No mobile or web targets yet
- No GPU acceleration (CPU-only with tiny-skia)

## Edge Cases

- CI should handle missing Rust version gracefully
- Dependencies should target compatible versions
- Cargo.lock must be committed for reproducible builds

## Acceptance Criteria

1. `cargo new ferrix-tasks` creates project with standard layout (`src/main.rs`, `src/lib.rs`)
2. Cargo.toml includes: Floem `0.2` (minor allowed), ezcal (round-trip + X-*), dirs, tracing, thiserror, anyhow
3. `rust-toolchain.toml` pins to current stable version
4. `.rustfmt.toml` configured for project style
5. `clippy.toml` or Cargo.toml clippy section configured
6. `.devcontainer/devcontainer.json` and `.devcontainer/Dockerfile` configured
7. Dockerfile includes: Rust toolchain, dev libs for wgpu/tiny-skia, Lapce binary
8. Wayland: Use direct Wayland socket (primary), XWayland as fallback
9. Container builds successfully with `devcontainer build`
10. CI Workflow `.github/workflows/ci.yml` exists with jobs: fmt → check → clippy → test → audit
11. CI runs on PR to main/master
12. `tests/ical_model_roundtrip.rs` contains integration test validating project's iCal model wrapper: parse → serialize → parse round-trip
13. Integration test validates custom X-* fields are preserved through round-trip via project's thin adapter layer
14. `cargo test` runs and passes (integration test validates scenario)
15. `cargo audit` runs as part of CI (warnings only, does not fail)
16. Cargo.lock committed to repository

## Dependencies

None - this is the first task.
