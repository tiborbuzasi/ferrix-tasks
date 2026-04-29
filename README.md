# Ferrix Tasks

An iCalendar-based todo/task application built with Rust and Floem.

## Features

- iCalendar (RFC 5545) based task storage
- Multiple calendar support (one .ics file per calendar)
- Desktop application (cross-platform: Linux, macOS, Windows)
- Local-first storage

## Quick Start

```bash
# Run the application
cargo run
```

## Development

### Dev Container

1. Open project in VS Code
2. Install Dev Containers extension
3. "Reopen in Container" when prompted

### Commands

```bash
# Format
cargo fmt

# Type check
cargo check

# Lint
cargo clippy

# Run tests
cargo test

# Build
cargo build
```

## Architecture

- **Storage**: iCalendar (.ics) files, one per calendar
- **UI**: Floem (native Rust GUI)
- **Model**: RFC 5545 VTODO components

## License

See [LICENSE](LICENSE) for details.