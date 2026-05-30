# Quickstart: Dark Fort RPG MVP

**Feature**: 001-dark-fort-rpg
**Date**: 2026-05-30

## Prerequisites

- Python 3.14
- [uv](https://docs.astral.sh/uv/) (package manager)
- Modern terminal with Unicode support (iTerm2, Terminal.app, Windows Terminal, kitty, alacritty)

## Setup

```bash
# Clone and enter project
cd return-to-dark-fort

# Install dependencies
uv sync

# Activate virtual environment (optional — uv run handles this)
# source .venv/bin/activate
```

## Running the Game

```bash
uv run dark-fort
# or: make run
```

## Development Commands

```bash
make help         # Show all available targets (auto-generated)
make check        # Full quality gate: lint + typecheck + test
make test         # Run all tests (unit + integration + e2e)
make test-unit    # Unit tests only
make test-integration  # Integration tests only (Textual UI tests)
make test-e2e     # End-to-end tests (full game flows)
make lint         # Run ruff check
make format       # Run ruff format
make typecheck    # Run ty type checker
make run          # Launch the game
make clean        # Remove __pycache__, .ruff_cache, .pytest_cache
```

## Project Layout

```
src/dark_fort/        # Application source
  app.py              # Textual App entry point
  models/             # Pydantic data models
  services/           # Game logic (interfaces + implementations)
  screens/            # Textual screens
  widgets/            # Reusable UI widgets
  persistence/        # Save/load game state

tests/
  unit/               # Pure logic tests
  integration/        # Screen ↔ service interaction tests
  e2e/                # Full game flow tests
```

## Architecture

- **Model layer**: Pydantic v2 — schema validation, JSON serialization for saves
- **Service layer**: Pure Python with ABC interfaces — dice rolling, combat, room generation, leveling, shop
- **UI layer**: Textual — reactive TUI with screens, modals, widgets
- **Persistence**: JSON file via Pydantic `.model_dump()` / `.model_validate()`

## Save Location

Game saves to `~/.dark_fort/save.json`. Delete this file to reset all progress.

## Running Tests

```bash
# All tests (TDD workflow)
make test

# Specific user story integration test
uv run pytest tests/integration/test_combat_screen.py -v

# E2E test for full game completion
uv run pytest tests/e2e/test_level_up_and_win.py -v
```
