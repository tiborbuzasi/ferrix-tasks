# Ferrix Tasks Development Agent Rules

## Core principle
Ferrix Tasks is a correctness-first, iCalendar-based system. Implementation must prioritize correctness, simplicity, and explicit behavior over flexibility or abstraction.

---

## System of truth

- Scenarios define correctness
- iCalendar defines persisted state
- Tasks define work units
- AGENTS.md defines system constraints

---

## Rule hierarchy (CRITICAL)

If rules conflict, precedence is:

1. Test scenarios (highest authority for correctness)
2. AGENTS.md system rules (engineering defaults + constraints)
3. Task specification (goal + scope + acceptance criteria)
4. Implementation notes (lowest authority)

If a scenario conflicts with AGENTS.md:
→ scenario defines correctness, AGENTS.md must be updated

---

## Engineering defaults (GLOBAL DECISIONS)

These are non-negotiable system-wide decisions:

### Runtime
- Tokio is the async runtime

### Logging / Observability
- Use `tracing` exclusively
- Structured logging is mandatory

### Error handling
- `thiserror` for domain/library errors
- `anyhow` for application-level errors

### Serialization / storage
- iCalendar (RFC 5545) is canonical storage format
- Preserve unknown X-* fields (lossless parsing where possible)

### Architecture style
- Modular monolith preferred
- Avoid premature workspace splitting

### Code style
- Prefer explicit over abstract
- Avoid unnecessary traits and overengineering

### Observability standard

- Ferrix uses `tracing` as the default logging and instrumentation framework
- All modules must use structured logging via `tracing`
- `log` crate should NOT be used unless explicitly required for compatibility

---

## Task execution rules (STRICT)

- NEVER implement functionality outside task scope
- NEVER add features not listed in acceptance criteria
- If requirements are ambiguous, incomplete, or contradictory → STOP and ask for clarification
- Prefer minimal correct solutions
- Do not refactor unrelated code
- Do not introduce abstractions unless required

---

## Task contract (mandatory structure)

Every task MUST contain:
- Goal
- Scope
- Acceptance criteria (mandatory)

If acceptance criteria are missing → task is invalid.

---

## Definition of Done

A task is complete only when:
- all acceptance criteria are satisfied
- no scope expansion occurred
- behavior is verifiable

---

## iCalendar rule

- iCalendar (RFC 5545) is canonical storage format
- Ferrix Task model is a projection of iCalendar, not a replacement
- Custom fields must use prefix: X-FERRIX-TASKS-*
- Unknown fields must be preserved during parsing
- All task state must be derivable from iCalendar representation

---

## iCalendar parsing layer

The system uses ezcal as a parsing and serialization adapter for iCalendar data.

This dependency is NOT considered the domain model.
Ferrix internal models are the source of truth.

The parsing layer may be replaced if it better satisfies:
- roundtrip correctness
- RFC 5545 adherence
- X-FERRIX-* preservation

---

## AI execution rule

- Task file is the ONLY source of truth
- Scenarios override tasks when conflicts exist
- Never infer missing requirements
- Never expand scope under “improvement” reasoning
- If a task attempts to override a system-level decision:
→ it must be rejected or escalated, not executed

---

## Documentation structure rules

- docs/tasks/<domain>/ for tasks
- docs/adr/ for architecture decisions
- docs/ideas/ for unstructured exploration

File naming:
- Tasks: FTX-XXX-short-title.md
- ADRs: ADR-XXX-short-title.md
  
---

## Testing & scenario system

- Test scenarios define system correctness
- Every acceptance criterion MUST trace to at least one scenario
- If no scenario exists, task-generator must propose one before execution
- Task-executor MUST implement tests that directly validate scenarios
- No test may exist without scenario traceability

---

## Dependency & versioning policy

- Use latest stable Rust toolchain
- Cargo.lock must be committed for applications
- Dependency versions MUST be pinned to minor version ranges (patch updates allowed, major updates require explicit decision)
- Avoid major version auto-upgrades in UI and parsing crates

---

## Decision escalation rules (CRITICAL)

The task-generator MUST NOT decide system-wide architecture choices.

The following decisions MUST be escalated to AGENTS.md (system defaults) and not resolved per-task:

- Rust version (MSRV)
- Dependency choices (top-level crates)
- Core crate selection (e.g., iCalendar, UI framework, async runtime)
- Architecture-wide libraries (logging, error handling, async runtime)
- Serialization and persistence formats

If any of these appear during task generation:
→ task-generator must STOP and request or reference AGENTS.md decision

---

## Version control responsibility

- Git branching, merging, and release management is strictly the responsibility of the developer
- AI agents must NOT perform or suggest branch operations unless explicitly asked
- All AI-generated changes are assumed to be committed within the current working branch context

---

## Task naming rules

- Task files use numeric ordering with underscore separator:
  001_short-description.md
  002_short-description.md

- No prefixes (e.g. FTX-, TASK-, etc.)
- Filenames must be lowercase and kebab-case after the numeric prefix
- Files use numeric prefixes only as stable identifiers (not execution order)
- The numeric prefix is immutable and serves only as a reference ID
- Execution order is NOT inferred from filename order
- Task execution is explicitly controlled via instruction or scenario selection
