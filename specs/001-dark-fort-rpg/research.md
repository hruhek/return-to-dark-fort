# Research: Dark Fort RPG MVP

**Feature**: 001-dark-fort-rpg
**Date**: 2026-05-30

## Decision: Textual TUI Framework

- **Decision**: Textual for terminal user interface
- **Rationale**: Textual provides a reactive component model (CSS-like styling, DOM-like widget tree) suited for game-like UIs with real-time updates. Rich TUI with keyboard navigation, modal screens, and animated widgets. Active ecosystem with mature testing support (`textual.testing`).
- **Alternatives considered**:
  - Rich + prompt_toolkit: Lower-level, more boilerplate for interactive apps
  - Curses directly: Too low-level, no component model, harder to test
  - Pygame: Full graphical engine, overkill for terminal-based RPG

## Decision: Pydantic v2 for Models and Serialization

- **Decision**: Pydantic v2 for all data models and game state persistence
- **Rationale**: Schema-first design with built-in validation, serialization (`.model_dump()` / `.model_validate()`), and JSON Schema generation. Perfect for game state save/load with type safety. Aligned with Interface-Driven Design principle — Pydantic models serve as the canonical data contracts.
- **Alternatives considered**:
  - dataclasses + manual validation: Too much boilerplate
  - attrs: No built-in serialization
  - msgspec: Faster but less ecosystem support, no Textual integration

## Decision: pytest + asyncio for Testing

- **Decision**: pytest with asyncio mode for all test types
- **Rationale**: Python's standard test framework. asyncio support needed because Textual apps are async. Textual's `pilot` fixture provides UI testing capabilities for integration and E2E tests.
- **Alternatives considered**:
  - unittest: Less ergonomic, harder parameterized testing
  - nose2: Unmaintained

## Decision: Textual UI Testing for Integration and E2E

- **Decision**: `textual.testing.Harness` + `pilot` for integration tests; snapshot testing for widget rendering
- **Rationale**: Textual's built-in testing harness allows pressing keys, clicking, and asserting widget states without headless browser. Integration tests verify screen ↔ service interactions. E2E tests run complete game flows through the Textual App.
- **Pattern**: Each integration test mounts a screen with a real service, interacts via pilot, asserts widget state. E2E tests mount the full `DarkFortApp` and drive game flows.
- **Alternatives considered**:
  - Mocking services in integration tests: Violates trace bullet principle — we need real component interactions
  - Selenium/Playwright for terminal: Not applicable

## Decision: Ruff for Linting and Formatting

- **Decision**: Ruff for both linting (`ruff check`) and formatting (`ruff format`)
- **Rationale**: Single tool for both concerns, extremely fast (Rust-based), replaces flake8 + isort + black. Supports pyproject.toml configuration. Pre-commit quality gate enforcement.
- **Alternatives considered**:
  - flake8 + black + isort: Three tools, slower, more config files
  - pylint: Heavier, slower

## Decision: ty for Type Checking

- **Decision**: ty for static type checking
- **Rationale**: Catches interface violations, Pydantic model misuse, and None-related errors before runtime. Required by pre-commit quality gate. Strict mode recommended.
- **Alternatives considered**:
  - mypy: More established but heavier; ty is lightweight and fast
  - pyright: Microsoft-maintained, good but requires node.js

## Decision: Make for Build Automation

- **Decision**: GNU Make with auto-generated help target
- **Rationale**: Universal availability on macOS/Linux, no extra dependencies. Help target parses `##` comments from other targets (self-documenting). Pattern: `make help` lists all targets automatically. Common targets: `test`, `lint`, `format`, `typecheck`, `check` (all gates), `run`, `clean`.
- **Alternatives considered**:
  - just: Requires separate installation, less universal
  - Taskfile: YAML-based, less familiar
  - tox/nox: Python-specific but heavier for simple task running

## Decision: JSON File Persistence

- **Decision**: JSON file on local filesystem for save/load
- **Rationale**: Pydantic models serialize natively to JSON. Single-player game — no database needed. File path configurable (default: `~/.dark_fort/save.json`). Save/load within 500ms performance target easily met.
- **Alternatives considered**:
  - SQLite: Overkill for single save file, adds dependency
  - pickle: Not safe, not portable across Python versions
  - TOML/YAML: No native Pydantic serialization support

## Decision: uv for Dependency and Project Management

- **Decision**: uv for virtual environment, dependency installation, and script running
- **Rationale**: Fast (Rust-based), unified tool for pip + venv + pyenv. `uv run` executes commands in the project environment. Python 3.14 support. Replaces pip + virtualenv + setuptools.
- **Alternatives considered**:
  - pip + venv: Slower, more commands, no lockfile
  - poetry: Heavier, slower resolution
  - pipenv: Less maintained
