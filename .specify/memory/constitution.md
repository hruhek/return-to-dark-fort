<!--
Sync Impact Report
==================
Version change: 0.0.0 (template placeholder) → 1.0.0
Bump rationale: MAJOR — initial constitution from empty template to fully defined governance with 6 principles.

Principles defined:
  I.   Test-Driven Development (NON-NEGOTIABLE)
  II.  Interface-Driven Design
  III. Integration Testing
  IV.  Trace Bullet Development (Vertical Slicing)
  V.   Green Tests Gate
  VI.  Pre-Commit Quality Gates

Sections added:
  - Development Workflow (feature implementation stages, design decision framework)
  - Quality Gates (per-commit, per-stage)

Templates:
  ✅ plan-template.md — Constitution Check section is dynamic; no changes needed
  ✅ spec-template.md — User stories + acceptance criteria already align with trace bullet and TDD
  ✅ checklist-template.md — No conflicts
  ⚠  tasks-template.md — "Tests are OPTIONAL" on line 12 conflicts with TDD principle; update required

Follow-up TODOs:
  - None; all placeholders resolved
-->

# Return to Dark Fort Constitution

## Core Principles

### I. Test-Driven Development (NON-NEGOTIABLE)

The TDD cycle MUST be strictly enforced for all code changes:

- Write a failing test before writing any production code
- Verify the test fails for the expected reason
- Implement the minimum code to make the test pass
- Refactor while keeping tests green

No production code may be written without a corresponding failing test first. Tests MUST cover both expected behavior and edge cases. The Red-Green-Refactor cycle is mandatory at every level (unit, integration, end-to-end).

**Rationale**: TDD ensures every line of code exists to satisfy a specific, testable requirement. It prevents regressions, produces a comprehensive test suite that serves as living documentation, and enables confident refactoring.

### II. Interface-Driven Design

All components MUST expose functionality through interfaces (abstract classes, protocols, or traits depending on language):

- Dependencies MUST be injected via interfaces, never through concrete class instantiation
- Gang of Four design patterns (Strategy, Observer, Factory, Command, etc.) MUST be applied where applicable
- Every service, repository, and core component MUST define a corresponding interface
- Interfaces enable testability via mocking/stubbing and loose coupling between components

**Rationale**: Interfaces decouple contracts from implementations, enabling independent testing, substitution of implementations, and adherence to the Open/Closed Principle.

### III. Integration Testing

Component interactions MUST be verified with integration tests:

- Integration tests MUST cover service-to-repository, service-to-service, and UI-to-service boundaries
- Contract tests MUST validate that interface implementations satisfy their contracts
- Integration test coverage is mandatory for all cross-component communication paths
- Integration tests MUST use real (not mocked) components within the slice being tested, stubbing only external boundaries

**Rationale**: Unit tests alone cannot verify that independently developed components work together correctly. Integration tests catch interface mismatches, serialization errors, and configuration issues that unit tests miss.

### IV. Trace Bullet Development (Vertical Slicing)

Every feature MUST be implemented using the trace bullet approach:

- Implementation MUST traverse all vertical layers: UI → Services → Core Components → Persistence
- Each vertical slice MUST deliver a working, demonstrable increment of functionality
- End-to-end tests MUST verify the complete slice before the next slice begins
- No layer may be stubbed or skipped at the expense of a complete, testable slice

**Rationale**: Vertical slicing ensures every feature is demonstrable from day one, catches integration issues early, and eliminates the "integration death march" that occurs when layers are built in horizontal isolation.

### V. Green Tests Gate

No development stage may be advanced until ALL tests pass:

- The full test suite (unit, integration, E2E) MUST be green before: code review, merging, or deployment
- A single failing test blocks all progress through subsequent development stages
- This gate applies at the feature, user story, and project level
- Regressions in previously green slices MUST be fixed before new work begins

**Rationale**: A green test suite is the only reliable signal that the software is in a working state. Allowing failures to accumulate erodes trust in the test suite and leads to an unreliable codebase.

### VI. Pre-Commit Quality Gates

Before each commit, ALL of the following MUST pass:

- Full test suite (unit, integration, E2E)
- Linter with zero errors
- Code formatter applied to all changed files

Commits that bypass these gates are prohibited. Automated pre-commit hooks MUST enforce these checks where supported by the toolchain.

**Rationale**: Every commit represents a checkpoint of known-good state. Enforcing quality at commit time prevents broken code from entering the shared history and ensures every commit is a valid rollback point.

## Development Workflow

### Feature Implementation Stages

1. **Specification**: Define user stories with acceptance criteria and priorities (P1-P3)
2. **Test Authoring**: Write failing tests for the complete vertical slice (unit → integration → E2E)
3. **Interface Design**: Define interfaces for all new components; select applicable GoF patterns
4. **Core Implementation**: Implement the slice bottom-up (persistence → core → services → UI)
5. **Integration**: Wire all layers; verify with integration tests
6. **E2E Verification**: Run end-to-end tests against the complete slice
7. **Quality Gate**: Run full test suite + linter + formatter
8. **Commit**: Commit passing code; proceed to next slice

### Design Decision Framework

- Prefer the simplest GoF pattern that solves the problem
- Deviations from interface-driven design require documented justification in the implementation plan
- Complex patterns (e.g., Visitor, Interpreter, Mediator) require explicit justification

## Quality Gates

### Per-Commit Gates
- `[ ]` Full test suite passes (unit + integration + E2E)
- `[ ]` Linter reports zero errors
- `[ ]` Code formatter applied to all modified files
- `[ ]` All public interfaces documented
- `[ ]` No debug code, print statements, or console logs in production paths

### Per-Stage Gates
- `[ ]` All tests for the current vertical slice pass
- `[ ]` Integration tests verify cross-component communication
- `[ ]` E2E test covers the complete user journey
- `[ ]` Previously completed slices remain green (no regressions)

## Governance

This Constitution supersedes all other development practices and preferences.

### Amendment Procedure
1. Proposed change documented with rationale and impact analysis
2. Review against existing principles for conflicts
3. Version increment per semantic versioning:
   - **MAJOR**: Principle removal or redefinition (backward incompatible)
   - **MINOR**: New principle added or section materially expanded
   - **PATCH**: Clarifications, wording fixes, non-semantic refinements
4. All dependent artifacts (templates, commands, docs) updated to reflect changes

### Compliance
All feature specifications, implementation plans, and code reviews MUST verify compliance with these principles. Any violation MUST be explicitly documented and justified in the Complexity Tracking section of the implementation plan.

**Version**: 1.0.0 | **Ratified**: 2026-05-30 | **Last Amended**: 2026-05-30
