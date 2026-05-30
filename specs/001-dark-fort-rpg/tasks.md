# Tasks: Dark Fort RPG MVP

**Input**: Design documents from `/specs/001-dark-fort-rpg/`

**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Tests are MANDATORY per the TDD principle in the Constitution. Every user story phase MUST include test tasks written BEFORE implementation tasks. Tests MUST fail before implementation begins.

## Format: `- [ ] [ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Source**: `src/dark_fort/` — single project (Textual TUI app)
- **Tests**: `tests/unit/`, `tests/integration/`, `tests/e2e/`

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization, tooling, and basic structure

- [ ] T001 Create project directory structure per plan.md: `src/dark_fort/{models,services,screens,widgets,persistence}`, `tests/{unit,integration,e2e}`
- [ ] T002 [P] Initialize `pyproject.toml` with Python 3.14, dependencies (textual, pydantic>=2, pytest, pytest-asyncio), and tool configs (ruff, ty)
- [ ] T003 [P] Create `Makefile` with `help` target auto-generated from `##` comments; targets: `check`, `test`, `test-unit`, `test-integration`, `test-e2e`, `lint`, `format`, `typecheck`, `run`, `clean`
- [ ] T004 [P] Configure ruff in `pyproject.toml` (line-length, target-version, lint rules, format rules)
- [ ] T005 [P] Configure ty in `pyproject.toml` (strict mode, Python 3.14 target)
- [ ] T006 Run `uv sync` to install dependencies and verify project builds

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T007 Define all Protocol interfaces in `src/dark_fort/services/interfaces.py`: `DiceRoller`, `CombatEngine`, `RoomGenerator`, `LevelingService`, `ShopService` (pure contracts, inherit `Protocol`, no implementation)
- [ ] T008 [P] Create `GameState` Pydantic model in `src/dark_fort/models/game_state.py` with fields: character, rooms, explored_room_ids, fled_room_ids, current_room_id, unscratched_benefits, turn
- [ ] T009 [P] Create `Character` + `Inventory` Pydantic models in `src/dark_fort/models/character.py` with all fields and validators (hp ≥ 0, silver ≥ 0, points ≥ 0)
- [ ] T010 [P] Create `Room`, `RoomShape`, `DoorCount`, `RoomContent` Pydantic models in `src/dark_fort/models/room.py`
- [ ] T011 [P] Create `Monster`, `SpecialAbility`, `LootItem` Pydantic models in `src/dark_fort/models/monster.py`
- [ ] T012 [P] Create `Weapon`, `Armor`, `Cloak`, `Potion`, `Scroll`, `Rope` Pydantic models in `src/dark_fort/models/items.py`
- [ ] T013 Implement `SaveManager` in `src/dark_fort/persistence/save_manager.py` using Pydantic `.model_dump()` / `.model_validate()` JSON round-trip; save path `~/.dark_fort/save.json`
- [ ] T014 [P] Write unit tests for all Pydantic model validators in `tests/unit/test_models.py` (hp caps, silver ≥ 0, cloak charges, scroll uses, room constraints)
- [ ] T015 [P] Write unit test for `SaveManager` round-trip in `tests/unit/test_save_manager.py` (save → load → identical GameState)
- [ ] T016 [P] Create `tests/conftest.py` with shared fixtures: sample `Character`, sample `Room`, sample `Monster`, temp save path
- [ ] T017 Create `DarkFortApp` shell in `src/dark_fort/app.py` — Textual App subclass with empty screen stack (screens added per user story)

**Checkpoint**: Foundation ready — user story implementation can now begin in parallel

---

## Phase 3: User Story 1 - Start a New Game (Priority: P1) 🎯 MVP

**Goal**: Player can launch the game, roll starting equipment, see character stats, enter the entrance room with its d4 outcomes

**Independent Test**: Launch app → TitleScreen appears → press N → character created with 15 HP, Silver = 15+d6, 1d4 weapon, 1d4 item → entrance room generated with d4 doors → content resolved (item/weak monster/scroll/quiet)

### Tests for User Story 1 (MANDATORY) ⚠️

> **NOTE: Write these tests FIRST per TDD. Ensure they FAIL before implementation.**

- [ ] T018 [P] [US1] Unit test for character creation logic in `tests/unit/test_character_creation.py` (verify HP=15, silver in [16,21], weapon and item from correct tables)
- [ ] T019 [P] [US1] Integration test for TitleScreen in `tests/integration/test_title_screen.py` (mount screen, verify N/C/Q keys, new game emits correct event)
- [ ] T020 [P] [US1] E2E test for new game flow in `tests/e2e/test_new_game.py` (full Textual app: launch → start → verify character + entrance room rendered)

### Implementation for User Story 1

- [ ] T021 [P] [US1] Implement `DiceRoller` Protocol implementation in `src/dark_fort/services/dice.py` (d4, d6, 2d6, modifiers, display format)
- [ ] T022 [US1] Implement character creation service in `src/dark_fort/services/character_factory.py` — creates Kargunt with 15 HP, Silver=15+d6, rolls 1d4 on Starting Weapons + Starting Items tables
- [ ] T023 [P] [US1] Create `StatBar` widget in `src/dark_fort/widgets/stat_bar.py` — displays HP bar, Silver, Points, attack bonus
- [ ] T024 [P] [US1] Create `DiceDisplay` widget in `src/dark_fort/widgets/dice_display.py` — animated dice roll rendering
- [ ] T025 [US1] Implement `TitleScreen` in `src/dark_fort/screens/title_screen.py` per contracts/screens.md — N=New Game, C=Continue (if save exists), Q=Quit
- [ ] T026 [US1] Implement entrance room logic in `src/dark_fort/services/room_generator.py` — entrance room only: 2d6 shape, d4 doors, 1d4 content (item/weak monster/scroll/quiet)
- [ ] T027 [US1] Implement `GameScreen` shell in `src/dark_fort/screens/game_screen.py` — room display, StatBar, door choices, action buttons (door actions only for US1)
- [ ] T028 [US1] Wire `DarkFortApp` screen stack in `src/dark_fort/app.py` — TitleScreen → GameScreen transition on new game, pass fresh GameState

**Checkpoint**: At this point, User Story 1 should be fully functional — player can start new game, see character, enter entrance room, resolve its content

---

## Phase 4: User Story 2 - Explore Rooms and Fight Monsters (Priority: P1)

**Goal**: Player explores rooms through doors, encounters monsters, runs full combat (attack/flee), manages health/silver/points, room map grows

**Independent Test**: From entrance → choose door → new room generated (shape + doors + content) → room content resolved → combat works for all 8 monster types → flee works → re-entry spawns 1-in-4 weak monster → points tracked → room count increments

### Tests for User Story 2 (MANDATORY) ⚠️

> **NOTE: Write these tests FIRST per TDD. Ensure they FAIL before implementation.**

- [ ] T029 [P] [US2] Unit test for room generation in `tests/unit/test_room_generator.py` (shape distribution, door distribution, content distribution)
- [ ] T030 [P] [US2] Unit test for combat engine in `tests/unit/test_combat.py` (hit/miss probabilities, weapon damage, unarmed d4-1, armor d4 per-hit, monster damage, flee d4, all 8 monster types)
- [ ] T031 [P] [US2] Integration test for GameScreen ↔ room exploration in `tests/integration/test_game_screen.py` (choose door, new room rendered, content interaction)
- [ ] T032 [P] [US2] Integration test for CombatScreen ↔ CombatEngine in `tests/integration/test_combat_screen.py` (attack, hit/miss, damage, monster HP, flee, death)
- [ ] T033 [US2] E2E test for explore + fight in `tests/e2e/test_explore_and_fight.py` (full flow: entrance → explore 3 rooms → fight at least 1 weak + 1 tough monster)

### Implementation for User Story 2

- [ ] T034 [US2] Implement `RoomGenerator` in `src/dark_fort/services/room_generator.py` — full room table (1d6: nothing, pit trap, soothsayer, weak monster, tough monster, void peddler), shape (2d6), doors (d4), re-entry 1-in-4 weak spawn
- [ ] T035 [US2] Implement `CombatEngine` in `src/dark_fort/services/combat.py` — hit roll (d6 vs points), weapon damage, unarmed d4-1, monster damage, armor per-hit d4, flee d4, death at 0 HP, loot on kill (silver, items per monster table), points awarded
- [ ] T036 [P] [US2] Create `RoomMap` widget in `src/dark_fort/widgets/room_map.py` — visual room graph, current room highlighted, explored/unexplored status
- [ ] T037 [P] [US2] Create `CombatLog` widget in `src/dark_fort/widgets/combat_log.py` — scrollable event feed (hit rolls, damage dealt, monster attacks, loot)
- [ ] T038 [US2] Implement `CombatScreen` modal in `src/dark_fort/screens/combat_screen.py` per contracts/screens.md — A=Attack, P=Potion, S=Scroll, F=Flee; monster HP bar + player HP bar + combat log
- [ ] T039 [US2] Implement `GameScreen` room exploration in `src/dark_fort/screens/game_screen.py` — door selection, room generation, combat trigger, content resolution, room map update, stat bar refresh
- [ ] T040 [US2] Wire `CombatScreen` ↔ `GameScreen` transition in `src/dark_fort/app.py` — on monster encounter push CombatScreen, on victory/flee/death pop back

**Checkpoint**: Player can explore multiple rooms, fight all 8 monster types, flee, see map grow, and track stats

---

## Phase 5: User Story 3 - Use Items, Scrolls, and Shop (Priority: P2)

**Goal**: Player uses potions to heal, activates scrolls for special effects, equips/unequips weapons and armor, buys items from Void Peddler

**Independent Test**: Use healing potion (d6 HP, ≤ max HP) → activate all 4 scroll types → equip different weapons (affect attack/damage) → visit Void Peddler → buy items with silver → inventory reflects correct state

### Tests for User Story 3 (MANDATORY) ⚠️

> **NOTE: Write these tests FIRST per TDD. Ensure they FAIL before implementation.**

- [ ] T041 [P] [US3] Unit test for inventory management in `tests/unit/test_inventory.py` (equip/unequip weapon, equip/unequip armor, use potion removes from inventory, scroll uses decrement, cloak charges decrement)
- [ ] T042 [P] [US3] Unit test for shop service in `tests/unit/test_shop.py` (buy with sufficient silver, reject with insufficient, item added to inventory, silver deducted)
- [ ] T043 [P] [US3] Unit test for scroll effects in `tests/unit/test_scrolls.py` (Summon Daemon d4 fights d4 dmg, Palms d6+1 d4 uses, Aegis -d4 d4 uses, False Omen choose room/reroll)
- [ ] T044 [P] [US3] Integration test for CharacterScreen ↔ Inventory in `tests/integration/test_character_screen.py` (open, view items, equip weapon, use potion, close)
- [ ] T045 [P] [US3] Integration test for ShopScreen ↔ ShopService in `tests/integration/test_shop_screen.py` (open shop, view prices, buy item, insufficient silver rejection)
- [ ] T046 [US3] E2E test for items + shop in `tests/e2e/test_items_and_shop.py` (full flow: find Void Peddler → buy items → use potion → activate scroll → verify effects)

### Implementation for User Story 3

- [ ] T047 [P] [US3] Implement `InventoryGrid` widget in `src/dark_fort/widgets/inventory_grid.py` — grid view of all carried items, equipped indicators, use/drop actions
- [ ] T048 [US3] Implement `CharacterScreen` modal in `src/dark_fort/screens/character_screen.py` per contracts/screens.md — inventory grid, equip/unequip, use potion, stat display
- [ ] T049 [US3] Implement scroll effects service in `src/dark_fort/services/scrolls.py` — Summon Weak Daemon (d4 fights, d4 damage), Palms Open the Southern Gate (d6+1, d4 uses), Aegis of Sorrow (-d4 per hit, d4 uses, combat duration), False Omen (choose room result OR reroll any die)
- [ ] T050 [US3] Implement `ShopService` in `src/dark_fort/services/shop.py` — Void Peddler price table lookup, purchase validation (sufficient silver), item instantiation
- [ ] T051 [US3] Implement `ShopScreen` modal in `src/dark_fort/screens/shop_screen.py` per contracts/screens.md — price table display, item selection, buy action, silver display
- [ ] T052 [US3] Wire potion/scroll usage into CombatScreen (P key = use potion, S key = use scroll) and GameScreen (U key = use item, I key = open CharacterScreen, $ key = open ShopScreen if Void Peddler)

**Checkpoint**: Full item economy functional — inventory management, all scroll effects, shop purchases, cloak charges

---

## Phase 6: User Story 4 - Level Up and Win (Priority: P2)

**Goal**: Player levels up via rooms+points or silver path, receives randomized benefits, tracks unscratched benefits, retires when all 6 earned

**Independent Test**: Explore 12 rooms + accumulate 15+ points → level up triggered (15 points deducted) → benefit rolled → OR collect 40+ silver → donate 40 → level up triggered → all 6 benefits applied correctly → retirement on 6th benefit

### Tests for User Story 4 (MANDATORY) ⚠️

> **NOTE: Write these tests FIRST per TDD. Ensure they FAIL before implementation.**

- [ ] T053 [P] [US4] Unit test for level-up service in `tests/unit/test_leveling.py` (rooms+points trigger, silver trigger, points deduction, silver deduction, benefit roll distribution, benefit application for all 6 types, unscratched tracking, retirement signaling)
- [ ] T054 [P] [US4] Integration test for LevelUpScreen in `tests/integration/test_level_up_screen.py` (benefit rolled, displayed, applied to character, screen dismisses)
- [ ] T055 [US4] E2E test for level-up + win in `tests/e2e/test_level_up_and_win.py` (full flow: explore 12 rooms, accumulate 15+ points → level up → apply 3 benefits → use silver path for 4th → earn all 6 → retire → congratulations)

### Implementation for User Story 4

- [ ] T056 [US4] Implement `LevelingService` in `src/dark_fort/services/leveling.py` — check both conditions (12 rooms + 15 points OR 40+ silver), deduct resources, roll 1d6 on unscratched benefits, apply benefit (Knighted=title, +1 attack=bonus, Max HP 20=cap, 5 potions=inventory, Mighty Zweihänder=inventory, halved damage=monster list), scratch result, detect retirement
- [ ] T057 [US4] Implement `LevelUpScreen` modal in `src/dark_fort/screens/level_up_screen.py` per contracts/screens.md — roll button, benefit display, monster chooser for benefit 6
- [ ] T058 [US4] Integrate level-up checks into `GameScreen` — after room cleared check rooms+points; after silver change check silver path; display "Donate 40 silver" action (L key) when silver ≥ 40
- [ ] T059 [US4] Implement retirement flow in `GameScreen` → retirement overlay with congratulations, auto-save, return to TitleScreen

**Checkpoint**: Full progression system — level up via both paths, all 6 benefits, retirement win condition

---

## Phase 7: User Story 5 - Death, Traps, and Special Monster Effects (Priority: P3)

**Goal**: Traps deal damage, rope mitigates, Soothsayer outcomes, Necro-Sorcerer death-ray alternation + maggot transformation, Medusa petrification, Basilisk immediate level-up, permadeath at 0 HP

**Independent Test**: Enter pit trap room → roll d6 (1-3 damage, +1 with rope) → fight Necro-Sorcerer (alternating death-ray, 1-in-6 maggot check every attack) → fight Medusa (1-in-6 petrify every attack) → kill Basilisk (2-in-6 immediate level-up) → reach 0 HP → death screen

### Tests for User Story 5 (MANDATORY) ⚠️

> **NOTE: Write these tests FIRST per TDD. Ensure they FAIL before implementation.**

- [ ] T060 [P] [US5] Unit test for trap + soothsayer logic in `tests/unit/test_traps.py` (pit trap d6 1-3 damage, rope +1, soothsayer separate d6 odd/even, odd choice silver/points, even d4 damage)
- [ ] T061 [P] [US5] Unit test for special monster abilities in `tests/unit/test_special_abilities.py` (death-ray alternation toggle, maggot 1-in-6, petrify 1-in-6, basilisk 2-in-6 level-up, stone troll 7 points on kill)
- [ ] T062 [P] [US5] E2E test for death + traps in `tests/e2e/test_death_and_traps.py` (full flow: encounter pit trap → survive → fight Necro-Sorcerer → survive death-ray → kill Basilisk → level up → die to Medusa → death screen → restart)

### Implementation for User Story 5

- [ ] T063 [P] [US5] Implement trap resolution in `src/dark_fort/services/room_generator.py` (add to existing file from T034) — pit trap (d6, 1-3 damage, +1 if rope, display rope benefit), soothsayer (separate d6, odd choice 10s/3pts, even d4 ignores armor)
- [ ] T064 [US5] Implement special monster abilities in `src/dark_fort/services/combat.py` — Necro-Sorcerer alternation (death_ray_alternation toggle), maggot transform 1-in-6 on every attack, Medusa petrify 1-in-6 on every attack, Basilisk immediate level-up 2-in-6 on kill
- [ ] T065 [US5] Implement death handling in `GameScreen` — 0 HP triggers death overlay, game over text, option to restart (clears save, returns to TitleScreen)
- [ ] T066 [US5] Update `CombatScreen` to display special ability warnings (death-ray active indicator, petrify risk hint, basilisk level-up chance) and apply effects in combat flow

**Checkpoint**: All special mechanics functional — traps, instant-death monster abilities, permadeath, game-over restart

---

## Phase 8: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] T067 [P] Implement `make run` target — `uv run dark-fort`
- [ ] T068 [P] Run `ruff check` and fix all lint errors
- [ ] T069 [P] Run `ruff format` on entire codebase
- [ ] T070 [P] Run `ty` type checker and fix all type errors
- [ ] T071 [P] Add keyboard shortcut hints to all screens (consistent footer bar)
- [ ] T072 Verify all 45 game-rules checklist items pass against final implementation (manual cross-check or automated rule audit)
- [ ] T073 Run full test suite (`make test`) — all unit + integration + e2e must be green
- [ ] T074 [P] Run `make check` — final quality gate (lint + typecheck + test)
- [ ] T075 Validate against quickstart.md — `uv sync && make run` works on clean checkout, `make test` passes
- [ ] T076 [P] Validate dice probability distributions per SC-006 — 10,000-sample statistical test for monster spawns, room contents, combat rolls, loot, and level-up benefits; assert within 5% tolerance of DARK FORT table probabilities

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion — BLOCKS all user stories
- **US1 (Phase 3)**: Depends on Foundational — enables GameScreen, TitleScreen, Character model, DiceRoller
- **US2 (Phase 4)**: Depends on Foundational; builds on US1's GameScreen, DiceRoller — enables CombatScreen, RoomGenerator, CombatEngine
- **US3 (Phase 5)**: Depends on Foundational + US2 (CombatScreen for potion/scroll in combat) — enables Inventory, Shop, Scrolls
- **US4 (Phase 6)**: Depends on Foundational + US2 (rooms, points from combat) — enables LevelingService, LevelUpScreen
- **US5 (Phase 7)**: Depends on Foundational + US2 (CombatEngine for special abilities) + US4 (Basilisk triggers level-up)
- **Polish (Phase 8)**: Depends on all user stories being complete

### User Story Dependencies

- **US1 (P1)**: Can start after Foundational — no dependencies on other stories
- **US2 (P1)**: Can start after Foundational — builds on US1's GameScreen/DiceRoller
- **US3 (P2)**: Depends on US2 (CombatScreen for in-combat item usage)
- **US4 (P2)**: Depends on US2 (combat for points, exploration for room count)
- **US5 (P3)**: Depends on US2 (CombatEngine) + US4 (Basilisk level-up)

### Within Each User Story

- Tests MUST be written and FAIL before implementation (TDD mandate)
- Models/Protocols before services
- Services/Widgets (parallel where marked [P]) before screens
- Screens before wiring (app.py screen stack)
- Story complete (tests green) before moving to next priority

### Parallel Opportunities

- All Setup tasks T002–T005 can run in parallel
- All Foundational model tasks T008–T012 can run in parallel with T007
- All tests within a user story marked [P] can run in parallel
- Widgets marked [P] within a story can run in parallel with services
- US1 and US2 models/services can run in parallel with their respective tests

---

## Parallel Example: User Story 1

```bash
# Launch all tests for User Story 1 together:
Task: "Unit test for character creation logic in tests/unit/test_character_creation.py"
Task: "Integration test for TitleScreen in tests/integration/test_title_screen.py"
Task: "E2E test for new game flow in tests/e2e/test_new_game.py"

# Launch all parallel implementation tasks together:
Task: "Implement DiceRoller Protocol implementation in src/dark_fort/services/dice.py"
Task: "Create StatBar widget in src/dark_fort/widgets/stat_bar.py"
Task: "Create DiceDisplay widget in src/dark_fort/widgets/dice_display.py"
```

## Parallel Example: User Story 2

```bash
# Launch all tests for User Story 2 together:
Task: "Unit test for room generation in tests/unit/test_room_generator.py"
Task: "Unit test for combat engine in tests/unit/test_combat.py"
Task: "Integration test for GameScreen ↔ room exploration in tests/integration/test_game_screen.py"
Task: "Integration test for CombatScreen ↔ CombatEngine in tests/integration/test_combat_screen.py"

# Launch parallel widgets together:
Task: "Create RoomMap widget in src/dark_fort/widgets/room_map.py"
Task: "Create CombatLog widget in src/dark_fort/widgets/combat_log.py"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL — blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Run `make test`, verify new game flow end-to-end
5. Demo: player can start game, see character, enter entrance room

### Incremental Delivery

1. Setup + Foundational → Foundation ready
2. Add US1 → Test independently → Demo (character creation + entrance room)
3. Add US2 → Test independently → Demo (exploration + combat)
4. Add US3 → Test independently → Demo (items + shop)
5. Add US4 → Test independently → Demo (leveling + win condition)
6. Add US5 → Test independently → Demo (traps + death + full game)
7. Polish → Run `make check` → Production-ready

### TDD Enforcement Per Story

1. Write test tasks (unit + integration + e2e for that story) → verify they FAIL
2. Implement models/services/widgets/screens for that story
3. Wire into app.py
4. Verify all tests for this story PASS
5. Verify NO regression in previous stories (`make test` must stay green)
6. Commit

---

## Notes

- [P] tasks = different files, no dependencies on incomplete tasks
- [Story] label maps task to specific user story for traceability
- Each user story should be independently completable and testable
- Verify tests fail before implementing (Red phase)
- Commit after each task or logical group (auto-commit enabled)
- Stop at any checkpoint to validate story independently
- Avoid: vague tasks, same file conflicts, cross-story dependencies that break independence
- Protocol interfaces are in `src/dark_fort/services/interfaces.py` — all services implement these
- Pydantic models are the canonical data contracts — use `.model_dump()` / `.model_validate()` for all serialization
