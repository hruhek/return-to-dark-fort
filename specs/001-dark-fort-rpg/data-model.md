# Data Model: Dark Fort RPG MVP

**Feature**: 001-dark-fort-rpg
**Date**: 2026-05-30
**Framework**: Pydantic v2

## Entity Relationship Overview

```
GameState (root, persisted to JSON)
├── Character (1)
│   ├── Inventory (1)
│   │   ├── Weapon? (0..1 equipped)
│   │   ├── Armor? (0..1 equipped)
│   │   ├── Cloak? (0..1)
│   │   ├── Potion[] (0..n)
│   │   ├── Scroll[] (0..n)
│   │   └── Rope[] (0..n)
│   └── ActiveScrollEffects[] (0..n)
├── RoomGraph
│   └── Room[] (1..n)
│       └── Monster? (0..1, active combat)
└── LevelUpTable (unscratched benefit tracking)
```

## Entities

### GameState

Top-level persistence envelope. Serialized to/from JSON.

| Field | Type | Description |
|-------|------|-------------|
| character | Character | Player character state |
| rooms | list[Room] | All generated rooms in graph |
| explored_room_ids | set[str] | IDs of fully explored rooms |
| fled_room_ids | set[str] | IDs of fled rooms (monster persists) |
| current_room_id | str \| None | Room player is currently in |
| unscratched_benefits | list[int] | Level-up benefits not yet earned (1-6) |
| turn | int | Monotonically increasing turn counter |

### Character

| Field | Type | Description |
|-------|------|-------------|
| name | str | "Kargunt" (or "Sir/Lady Kargunt" after benefit 1) |
| hp | int | Current hit points (0 = death) |
| max_hp | int | Max HP (starts 15, benefit 3 → 20) |
| silver | int | Current silver (non-negative) |
| points | int | Accumulated monster points |
| attack_bonus | int | Permanent attack modifier (benefit 2 → +1) |
| inventory | Inventory | All carried items |
| active_scrolls | list[ActiveScrollEffect] | Currently active scroll effects |
| halved_damage_monsters | list[str] | Monster names with halved damage (benefit 6) |

### Inventory

| Field | Type | Description |
|-------|------|-------------|
| equipped_weapon | Weapon \| None | Currently equipped weapon |
| equipped_armor | Armor \| None | Currently equipped armor |
| cloak | Cloak \| None | Cloak of invisibility with remaining charges |
| potions | list[Potion] | Healing potions |
| scrolls | list[Scroll] | Scrolls with uses remaining |
| ropes | list[Rope] | Ropes (count tracked by list length) |

### Weapon (Literal type)

| Field | Type | Description |
|-------|------|-------------|
| name | Literal["Dagger", "Warhammer", "Sword", "Flail", "Mighty Zweihänder"] | Weapon name |
| damage_dice | str | e.g. "d4", "d6", "d6+1", "d6+2" |
| attack_modifier | int | +1 for Dagger, Sword; 0 for others |

### Armor

Marker item — presence in `equipped_armor` enables d4 per-hit absorption.

### Cloak

| Field | Type | Description |
|-------|------|-------------|
| charges | int | Remaining fight avoidances (1-4) |

### Potion

Marker item — using one heals d6 HP (max HP cap enforced at use time).

### Scroll

| Field | Type | Description |
|-------|------|-------------|
| name | Literal["Summon weak daemon", "Palms Open the Southern Gate", "Aegis of Sorrow", "False Omen"] | Scroll type |
| remaining_uses | int | Uses left (1 for daemon/False Omen; d4 for Palms/Aegis) |

### Rope

Marker item — passive +1 to pit trap roll while in inventory.

### ActiveScrollEffect

| Field | Type | Description |
|-------|------|-------------|
| scroll_name | str | Which scroll is active |
| fights_remaining | int | Fights left for daemon; 0 for other scroll types |
| damage_reduction | int | For Aegis: d4 reduction per hit for remainder of combat |

### Room

| Field | Type | Description |
|-------|------|-------------|
| id | str | Unique room identifier (UUID) |
| shape | RoomShape | From 2d6 table |
| doors | DoorCount | 0, 1, or 2 doors |
| content | RoomContent | What happens when entered |
| connected_rooms | list[str] | IDs of connected rooms |
| monster | Monster \| None | Active monster (if room has monster and not yet killed) |
| exploration_status | Literal["unexplored", "explored", "fled"] | Current status |

### RoomShape (Enum)

```
IRREGULAR_CAVE, OVAL, CROSS, CORRIDOR, SQUARE, ROUND, RECTANGULAR, TRIANGULAR, SKULL
```

### DoorCount (Enum)

```
ZERO = 0  # Dead end
ONE = 1
TWO = 2
```

### RoomContent

| Field | Type | Description |
|-------|------|-------------|
| type | Literal["nothing", "pit_trap", "soothsayer", "weak_monster", "tough_monster", "void_peddler"] | Content type |
| monster_type | str \| None | Which monster spawned (if any) |
| resolved | bool | Has player resolved this room's content? |

### Monster

| Field | Type | Description |
|-------|------|-------------|
| name | str | Monster name |
| category | Literal["weak", "tough"] | Category |
| points | int | Hit threshold |
| damage_dice | str | Damage expression |
| hp | int | Current HP |
| max_hp | int | Max HP |
| special_ability | SpecialAbility \| None | Special ability config |
| loot_silver | int \| None | Silver on kill |
| loot_item | LootItem \| None | Item on kill (with probability) |
| death_ray_alternation | bool | True if next attack is death-ray (Necro-Sorcerer only) |

### SpecialAbility

| Field | Type | Description |
|-------|------|-------------|
| type | Literal["death_ray", "maggot_transform", "petrify", "level_up_on_kill"] | Ability type |
| trigger | Literal["every_attack", "on_kill"] | When it fires |
| probability | str | "1-in-6" or "2-in-6" |
| effect | str | "game_over" or "level_up" |

### LootItem

| Field | Type | Description |
|-------|------|-------------|
| item_name | str | "Dagger", "Random scroll", "Rope" |
| probability | str | "2-in-6" (only for weak monsters) |

### Level-Up Benefit

Predefined table (never persisted — unscratched tracking is in GameState):

| Roll | Benefit | Effect |
|------|---------|--------|
| 1 | Knighted | name → "Sir/Lady Kargunt" |
| 2 | +1 attack | character.attack_bonus += 1 |
| 3 | Max HP 20 | character.max_hp = 20 |
| 4 | Gain 5 potions | inventory.potions += 5 |
| 5 | Mighty Zweihänder | inventory add Mighty Zweihänder |
| 6 | Halved damage | Choose 1 weak + 1 tough; add to halved_damage_monsters |

### Validation Rules

- **Character**: hp ∈ [0, max_hp]; silver ≥ 0; points ≥ 0
- **Cloak**: charges ∈ [0, 4]; discarded when charges = 0
- **Scroll**: remaining_uses ≥ 0; discarded when 0
- **Room**: exactly 1 entrance room with `id == "entrance"`
- **Monster**: hp ∈ [0, max_hp]; removed from room when hp ≤ 0
- **GameState**: unscratched_benefits ⊆ {1,2,3,4,5,6}; no duplicates
- **RoomGraph**: connected_rooms must reference real Room IDs; no orphan references

### State Transitions

```
GameState Lifecycle:
  NEW → (character created, entrance room generated)
  PLAYING → (rooms explored, combat resolved, items used)
  LEVELED_UP → (benefit applied, return to PLAYING)
  DEAD → (hp = 0, game over, restart option)
  RETIRED → (all 6 benefits scratched, congratulations)

Room Lifecycle:
  UNEXPLORED → (entered)
  EXPLORED → (content resolved: monster killed, trap survived, soothsayer answered, shop visited, or nothing)
  or
  UNEXPLORED → (entered, fled)
  FLED → (re-entered → UNEXPLORED with 1-in-4 weak monster spawn)

Combat Lifecycle:
  ENGAGED → (monster present in room)
  IN_PROGRESS → (attack/flee/use item each round)
  VICTORY → (monster hp ≤ 0, points + loot awarded, room → EXPLORED)
  FLEE → (player takes d4 damage, room → FLED)
  DEATH → (player hp ≤ 0, game → DEAD)
```
