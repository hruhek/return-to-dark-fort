# Implementation Plan: Dark Fort RPG MVP

**Branch**: `001-dark-fort-rpg` | **Date**: 2026-05-30 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `/specs/001-dark-fort-rpg/spec.md`

## Summary

Implement a single-player dungeon crawler RPG as a Textual TUI application, faithfully implementing all DARK_FORT.md rules. Player character Kargunt explores procedurally generated rooms, fights monsters with dice-based combat, manages inventory, buys items from the Void Peddler, and levels up through 6 distinct benefits. Game state persists locally via JSON serialization with Pydantic models.

Technical approach: Model → Service → Screen vertical slices per the trace bullet principle. Every component exposes an interface via `typing.Protocol` (structural subtyping, no explicit inheritance). TDD enforced: unit tests for pure logic, Textual UI integration tests for component interaction, E2E tests for full game flows.

## Technical Context

**Language/Version**: Python 3.14

**Primary Dependencies**: Textual (TUI framework), Pydantic (schema validation/serialization)

**Storage**: JSON file (local filesystem) via Pydantic model serialization

**Testing**: pytest + asyncio; Textual UI testing for integration and E2E; unit tests for pure logic

**Target Platform**: Terminal (macOS, Linux, WSL) — any platform with Python 3.14 and a modern terminal

**Project Type**: TUI application (Textual)

**Performance Goals**: Dice roll results displayed within 200ms of input; room generation within 100ms; save/load within 500ms

**Constraints**: Single-player local-only; no network; offline-capable; terminal 80×24 minimum

**Scale/Scope**: Single codebase; ~15 Textual screens/widgets; ~10 Pydantic models; ~8 service modules; ~18 functional requirements

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

### I. Test-Driven Development (NON-NEGOTIABLE)

✅ **Plan**: All code written after failing tests. Unit tests for services (`tests/unit/`). Integration tests via Textual UI testing for component interaction (`tests/integration/`). E2E tests for full game flows (`tests/e2e/`). Commands: `make test`, `make test-unit`, `make test-integration`, `make test-e2e`.

### II. Interface-Driven Design

✅ **Plan**: Every service exposes a Protocol inheriting from `typing.Protocol` (structural subtyping, pure contract). ABC used only for base classes with shared behavior. GoF patterns: Strategy (dice mechanics), Command (player actions), Observer (game state changes → UI updates), Factory (monster/room generation). Dependencies injected via constructor.

### III. Integration Testing

✅ **Plan**: Textual UI tests verify component interaction: `CombatScreen` ↔ `CombatEngine`, `GameScreen` ↔ `RoomGenerator`, `CharacterScreen` ↔ `Inventory`, `ShopScreen` ↔ `ShopService`. Each user story's "Independent Test" becomes an integration test.

### IV. Trace Bullet Development (Vertical Slicing)

✅ **Plan**: Each user story implemented as a vertical slice from screen → service → model → persistence:
- US1 (P1): Title screen → Character model → Character persistence → New game flow
- US2 (P1): Game screen → Room generator → Room model → Combat engine → Map widget
- US3 (P2): Inventory widget → Item models → Shop screen → Scroll effects
- US4 (P2): Level-up logic → Level-up screen → Benefit application → Character persistence → Win condition
- US5 (P3): Trap effects → Special monster abilities → Death/permadeath → Game over

### V. Green Tests Gate

✅ **Plan**: `make test` runs full suite (unit + integration + E2E). No stage advances without a green suite. CI-ready: `make check` = `make lint && make typecheck && make test`.

### VI. Pre-Commit Quality Gates

✅ **Plan**: `make check` runs ruff check + ruff format --check + ty + pytest. Enforced via `make all` as the gate. Pre-commit hooks optional but documented.

**Gate Result**: PASS — all principles have concrete implementation strategies; no violations.

## Project Structure

### Documentation (this feature)

```text
specs/001-dark-fort-rpg/
├── spec.md              # Feature specification
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── contracts/           # Phase 1 output (UI screen contracts)
│   └── screens.md       # Screen interface contracts
├── checklists/
│   ├── requirements.md  # Spec quality checklist
│   └── game-rules.md    # Rules completeness checklist
└── tasks.md             # Phase 2 output (NOT created by /speckit.plan)
```

### Source Code (repository root)

```text
src/dark_fort/
├── __init__.py
├── app.py                    # DarkFortApp(App) — Textual entry point, screen stack
├── models/
│   ├── __init__.py
│   ├── character.py          # Character, Inventory (Pydantic)
│   ├── monster.py            # Monster, SpecialAbility, LootItem (Pydantic)
│   ├── room.py               # Room, RoomContent, RoomShape, DoorCount (Pydantic)
│   ├── items.py              # Weapon, Scroll, Potion, Armor, Cloak, Rope (Pydantic)
│   └── game_state.py         # GameState — save/load envelope (Pydantic)
├── services/
│   ├── __init__.py
│   ├── interfaces.py         # Protocols: DiceRoller, CombatEngine, RoomGenerator, etc.
│   ├── dice.py               # DiceRoller implementation
│   ├── combat.py             # CombatEngine implementation
│   ├── room_generator.py     # RoomGenerator implementation
│   ├── leveling.py           # LevelingService implementation
│   └── shop.py               # ShopService implementation
├── screens/
│   ├── __init__.py
│   ├── title_screen.py       # New game / continue
│   ├── game_screen.py        # Main game: map, room display, actions
│   ├── combat_screen.py      # Combat encounter modal
│   ├── character_screen.py   # Character sheet / inventory management
│   ├── shop_screen.py        # Void Peddler shop modal
│   └── level_up_screen.py    # Level-up benefit display
├── widgets/
│   ├── __init__.py
│   ├── dice_display.py       # Animated dice roll widget
│   ├── stat_bar.py           # HP / Silver / Points header bar
│   ├── room_map.py           # Room graph visualizer
│   ├── combat_log.py         # Scrollable combat event log
│   └── inventory_grid.py     # Inventory grid widget
└── persistence/
    ├── __init__.py
    └── save_manager.py       # GameState ↔ JSON serialization

tests/
├── conftest.py               # Shared fixtures, Textual test harness setup
├── unit/
│   ├── test_dice.py
│   ├── test_combat.py
│   ├── test_room_generator.py
│   ├── test_leveling.py
│   ├── test_shop.py
│   └── test_models.py        # Pydantic model validation tests
├── integration/
│   ├── test_combat_screen.py        # CombatScreen ↔ CombatEngine interaction
│   ├── test_game_screen.py          # GameScreen ↔ RoomGenerator interaction
│   ├── test_character_screen.py     # CharacterScreen ↔ Inventory interaction
│   ├── test_shop_screen.py          # ShopScreen ↔ ShopService interaction
│   └── test_save_load.py            # Persistence round-trip
└── e2e/
    ├── test_new_game.py             # US1: Start → entrance room
    ├── test_explore_and_fight.py    # US2: Explore rooms + combat loop
    ├── test_items_and_shop.py       # US3: Items, scrolls, shop
    ├── test_level_up_and_win.py     # US4: Level-up → retirement
    └── test_death_and_traps.py      # US5: Traps, special effects, death

Makefile
pyproject.toml
```

**Structure Decision**: Single TUI project (`src/dark_fort/`) with models–services–screens–widgets–persistence layers. Tests mirror the source tree split by test type (unit/integration/e2e). No web backend needed — state persistence is file-based JSON.

## Complexity Tracking

> No violations to justify — all constitution principles pass.
