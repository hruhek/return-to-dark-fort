# UI Screen Contracts: Dark Fort RPG MVP

**Feature**: 001-dark-fort-rpg
**Date**: 2026-05-30

Each screen exposes a public interface contract: what services it depends on, what UI events it emits, and what state it renders from.

## TitleScreen

**Purpose**: New game / continue / quit.

**Dependencies**: `SaveManager` (check if save exists)

**Inputs**:
- `[N]` — Start new game
- `[C]` — Continue (only if save exists)
- `[Q]` — Quit

**Events emitted**:
- `NewGame` → triggers `GameScreen` with fresh `Character` + entrance `Room`
- `LoadGame` → triggers `GameScreen` with loaded `GameState`
- `Quit` → exits app

**States**:
| State | Conditions |
|-------|------------|
| No save | Only "New Game" and "Quit" visible |
| Save exists | "Continue" visible with character summary |

---

## GameScreen

**Purpose**: Main gameplay — displays room, stats, available actions, room map.

**Dependencies**: `RoomGenerator`, `CombatEngine`, `DiceRoller`, `LevelingService`

**Inputs**:
- `[1-4]` — Choose door (if available)
- `[A]` — Attack (if monster present)
- `[F]` — Flee (if monster present)
- `[U]` — Use item (opens inventory picker)
- `[I]` — Open CharacterScreen (inventory/equipment)
- `[$]` — Open ShopScreen (if Void Peddler present)
- `[S]` — Save game
- `[L]` — Donate 40 silver (level-up via silver path)
- `[Esc]` — Return to TitleScreen

**Events emitted**:
- `RoomEntered(Room)` → new room generated, content displayed
- `CombatStarted(Monster)` → combat engagement
- `CombatEnded(Result)` → victory/flee/death
- `LevelUp(benefit)` → level-up triggered
- `GameOver` → death, trigger death screen
- `Retired` → all benefits earned, trigger congratulations

**States**:
| State | Conditions |
|-------|------------|
| Exploring | No monster in room; doors displayed |
| Combat | Monster present; combat actions displayed |
| Shopping | Void Peddler present; shop actions |
| Dead | HP = 0; game over overlay |
| Retired | All 6 benefits scratched; congratulations |

---

## CombatScreen (Modal)

**Purpose**: Turn-based combat against a monster.

**Dependencies**: `CombatEngine`, `DiceRoller`

**Inputs**:
- `[A]` — Attack (roll d6)
- `[P]` — Use potion (if available)
- `[S]` — Use scroll (if applicable in combat)
- `[F]` — Flee (take d4 damage)

**Events emitted**:
- `AttackRolled(player_roll, hit, damage_dealt)`
- `MonsterAttack(damage_taken, armor_absorbed)`
- `SpecialAbilityTriggered(ability, result)`
- `MonsterKilled(loot)`
- `Fled(damage_taken)`
- `PlayerDied`

**Rendered state**: Monster name, HP bars (player + monster), combat log, action buttons.

---

## CharacterScreen (Modal)

**Purpose**: View/set character stats, manage inventory, equip items.

**Dependencies**: `Character` model directly

**Inputs**:
- `[E]` — Equip/unequip weapon
- `[U]` — Use potion (out of combat)
- `[D]` — Drop item (if inventory management needed)
- `[Esc]` — Close

**Rendered state**: HP (current/max), Silver, Points, Attack bonus, Equipped weapon, Armor status, Inventory list, Active effects.

---

## ShopScreen (Modal)

**Purpose**: Void Peddler shop — buy items with silver.

**Dependencies**: `ShopService`, `Character`

**Inputs**:
- `[1-9]` — Select item from price list
- `[B]` — Buy selected item
- `[Esc]` — Leave shop

**Events emitted**:
- `ItemPurchased(item, cost)` → item added to inventory, silver deducted
- `CannotAfford` → item too expensive

**Rendered state**: Void Peddler price table, player silver, purchase prompt.

---

## LevelUpScreen (Modal)

**Purpose**: Display level-up benefit after trigger.

**Dependencies**: `LevelingService`

**Inputs**:
- `[Enter]` — Roll for benefit
- For benefit 6: `[1-4]` select weak monster, `[1-4]` select tough monster

**Events emitted**:
- `BenefitRolled(benefit)` → benefit applied to character
- `AllBenefitsEarned` → trigger retirement

---

## Screen Transition Contract

```
TitleScreen
  ├─ NewGame ──────────→ GameScreen (exploring)
  └─ Continue ────────→ GameScreen (exploring)

GameScreen
  ├─ Monster present ─→ CombatScreen ←→ GameScreen
  ├─ Void Peddler ────→ ShopScreen ←→ GameScreen
  ├─ [I] key ─────────→ CharacterScreen ←→ GameScreen
  ├─ Level up ────────→ LevelUpScreen → GameScreen
  ├─ HP = 0 ──────────→ DeathOverlay → TitleScreen
  └─ Retired ─────────→ RetiredOverlay → TitleScreen
```
