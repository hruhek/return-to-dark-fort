# UI Layout Contracts: Dark Fort RPG MVP

**Feature**: 001-dark-fort-rpg
**Date**: 2026-05-30
**Framework**: Textual CSS (Grid-based layout)

## Screen Layouts

### TitleScreen

```
┌─────────────────────────────────────────────┐
│                                             │
│                                             │
│         ╔═══════════════════════╗            │
│         ║     DARK FORT        ║            │
│         ║  Return to Darkness  ║            │
│         ╚═══════════════════════╝            │
│                                             │
│            [N] New Game                     │
│            [C] Continue  (if save exists)    │
│            [Q] Quit                         │
│                                             │
│        Kargunt the Catacomb Rogue           │
│        Save: 3 rooms | 12 pts | 28s         │
│            (only when save exists)           │
│                                             │
└─────────────────────────────────────────────┘
```

**Widgets**: `Label` (title), `Label` (subtitle), `Static` (menu options), `Static` (save summary)

**CSS Grid**: Single column, vertically centered. Title area takes 1/3 height, menu 1/3, save info 1/3.

```css
TitleScreen {
    align: center middle;
    grid-size: 1;
    grid-rows: 1fr auto 1fr;
}
```

---

### GameScreen

```
┌─────────────────────────────────────────────┐
│ HP ██████████████░░░ 12/15 │ 💰 22s │ ★ 8pts│ ← StatBar
├─────────────────────────────────────────────┤
│                                             │
│   ┌──────────┐     ┌──────────┐             │
│   │ Entrance  │─────│  Room 2  │   ← RoomMap │
│   │   □       │     │   ?      │             │
│   └──────────┘     └──────────┘             │
│        │                                     │
│   ┌──────────┐                               │
│   │  Room 3  │ (current)                     │
│   │   ⚔       │                               │
│   └──────────┘                               │
│                                             │
├─────────────────────────────────────────────┤
│ 🚪 South Door  🚪 East Door                 │  ← Actions
│ [U] Items  [I] Inventory  [$] Shop  [S] Save │
└─────────────────────────────────────────────┘
```

**Widgets**: `StatBar` (header), `RoomMap` (left portion of center), `RoomInfo` panel (right portion — room description, shape, doors, content), `ActionBar` (footer)

**CSS Grid**: 3 rows — header (3 rows), content (1fr), footer (3 rows). Content row: 2 columns — RoomMap (2/3 width), RoomInfo (1/3 width).

```css
GameScreen {
    grid-size: 2;
    grid-rows: auto 1fr auto;
    grid-columns: 2fr 1fr;
}

#stat-bar { column-span: 2; height: 3; }
#room-map { row: 2; column: 0; }
#room-info { row: 2; column: 1; border: solid $primary; }
#actions { column-span: 2; height: auto; }
```

**RoomInfo panel content** (varies by room content):
- Empty room: "The room is quiet. Explored."
- Pit trap: "A pit opens beneath you! Roll d6..."
- Soothsayer: "A hooded figure speaks in riddles..."
- Monster: Monster name, points, HP bar, "Prepare for battle!"
- Void Peddler: "A twisted merchant beckons... [Enter shop]"

---

### CombatScreen (Modal — overlays GameScreen)

```
┌─────────────────────────────────────────────┐
│ ⚔ Necro-Sorcerer │ HP ████░░░░ 6/8          │ ← MonsterBar
├─────────────────────────────────────────────┤
│  Kargunt  │ HP ██████████░░ 12/15 │ 🛡 Armor │ ← PlayerBar
├─────────────────────────────────────────────┤
│                                             │
│  Round 3                                    │
│  ▶ You roll 4 + 0 = 4 vs 4 pts → HIT!      │ ← CombatLog
│  ▶ You deal 5 damage (Sword d6)             │
│  ▶ Necro-Sorcerer attacks! d4 damage        │
│  ▶ Armor absorbs 2 → You take 0             │
│  ⚠ Death-ray next! (d6 damage)              │
│                                             │
├─────────────────────────────────────────────┤
│ [A]ttack  [P]otion  [S]croll  [F]lee         │ ← CombatActions
└─────────────────────────────────────────────┘
```

**Widgets**: `MonsterBar` (name, points, HP bar), `PlayerBar` (name, HP bar, armor indicator, active effects), `CombatLog` (scrollable RichLog), `CombatActions` (footer buttons)

**CSS Grid**: 4 rows — MonsterBar (3), PlayerBar (3), CombatLog (1fr), CombatActions (3).

```css
CombatScreen {
    grid-size: 1;
    grid-rows: auto auto 1fr auto;
}

#monster-bar { height: 3; border: solid red; }
#player-bar { height: 3; border: solid green; }
#combat-log { border: solid $primary-darken-2; overflow-y: scroll; }
#combat-actions { height: 3; align: center middle; }
```

**Special ability indicators** in MonsterBar:
- "⚡ DEATH-RAY ACTIVE" (Necro-Sorcerer on death-ray turn)
- "🐍 Petrify risk!" (Medusa, always shown)
- "⭐ Level-up on kill!" (Basilisk, always shown)

---

### CharacterScreen (Modal)

```
┌─────────────────────────────────────────────┐
│ ═══════════ CHARACTER ═══════════            │
│                                             │
│  Sir Kargunt  │  HP 14/20                    │
│  Attack +1     │  Silver 34s                 │
│               │  Points 12                  │
│               │  Rooms 5                    │
│  ─────────────────────────────              │
│  ⚔ Sword (d6, +1) [E] Unequip              │ ← Equipment
│  🛡 Armor (-d4)    [E] Unequip              │
│  ─────────────────────────────              │
│  Inventory:                                 │
│  🧪 Potion ×2       [U] Use                 │ ← Inventory
│  📜 Summon Daemon   [U] Use                 │
│  📜 False Omen      [U] Use                 │
│  🪢 Rope ×1                                 │
│  👻 Cloak (2/4 charges)                     │
│  ─────────────────────────────              │
│  Effects: Aegis of Sorrow (2 uses left)      │ ← ActiveEffects
│                                             │
│                [Esc] Close                   │
└─────────────────────────────────────────────┘
```

**Widgets**: `CharacterStats` (name, HP, silver, points, rooms, attack), `EquipmentPanel` (weapon/armor with equip toggle), `InventoryGrid` (scrollable item list with use/drop), `ActiveEffectsPanel` (scroll effect status)

**CSS Grid**: 2 columns — left (stats + equipment), right (inventory + effects).

```css
CharacterScreen {
    grid-size: 2;
    grid-columns: 1fr 1fr;
    grid-rows: auto 1fr auto;
}

#char-stats { column: 0; row: 0; }
#equipment { column: 0; row: 1; border: solid $secondary; }
#inventory { column: 1; row: 0; row-span: 2; border: solid $secondary; }
#active-effects { column: 0; row: 2; }
```

---

### ShopScreen (Modal)

```
┌─────────────────────────────────────────────┐
│         ═══════ VOID PEDDLER ═══════         │
│         "Buy something... or leave."         │
│                                             │
│  Your Silver: 34s                           │
│  ─────────────────────────────────          │
│  Item                    │ Cost  │ Buy      │
│  ────────────────────────│───────│──────    │
│  Potion (heal d6)        │   4s  │ [1]      │ ← PriceList
│  Random scroll           │   7s  │ [2]      │
│  Dagger (d4, +1)         │   6s  │ [3]      │
│  Warhammer (d6)          │   9s  │ [4]      │
│  Rope                    │   5s  │ [5]      │
│  Sword (d6, +1)          │  12s  │ [6]      │
│  Flail (d6+1)            │  15s  │ [7]      │
│  Mighty Zweihänder(d6+2) │  25s  │ [8]      │
│  Armor (-d4)             │  10s  │ [9]      │
│  Cloak of invisibility   │  15s  │ [0]      │
│  ─────────────────────────────────          │
│                                             │
│            [Esc] Leave shop                  │
└─────────────────────────────────────────────┘
```

**Widgets**: `Label` (peddler title + flavor text), `Label` (silver display), `DataTable` (price list), `Label` (leave hint)

**CSS Grid**: Single column. Silver at top, DataTable fills center, footer hint at bottom.

```css
ShopScreen {
    grid-size: 1;
    grid-rows: auto auto 1fr auto;
}

#shop-title { height: 3; align: center middle; }
#silver-display { height: 1; }
#price-table { border: solid $accent; }
#shop-footer { height: 1; align: center middle; }
```

**Affordability highlighting**: Items player can't afford are dimmed (`$text-disabled`); affordable items are bright (`$text-success`).

---

### LevelUpScreen (Modal)

```
┌─────────────────────────────────────────────┐
│                                             │
│         ╔═══════════════════════╗            │
│         ║    LEVEL UP!         ║            │
│         ╚═══════════════════════╝            │
│                                             │
│          [Enter] Roll for benefit            │
│                                             │
│  ─── After rolling ───                      │
│                                             │
│         Rolled 3: Max HP becomes 20!         │ ← BenefitDisplay
│         Your max HP is now 20.              │
│                                             │
│  ─── For benefit 6 only ───                 │
│                                             │
│  Choose Weak monster:  [1] Skel [2] Cult     │ ← MonsterChooser
│                        [3] Gobl  [4] Hound   │
│  Choose Tough monster: [1] Necro [2] Troll   │
│                        [3] Medusa [4] Basil   │
│                                             │
│         [Enter] Continue                     │
└─────────────────────────────────────────────┘
```

**Widgets**: `Label` (title), `Label` (prompt before roll), `BenefitDisplay` (after roll — benefit name + description), `MonsterChooser` (only for benefit 6 — two columns of monster names), `Label` (continue prompt)

**CSS Grid**: Single column, vertically centered.

```css
LevelUpScreen {
    align: center middle;
    grid-size: 1;
    grid-rows: auto auto 1fr auto;
}
```

---

## Widget Reuse Across Screens

| Widget | TitleScreen | GameScreen | CombatScreen | CharacterScreen | ShopScreen | LevelUpScreen |
|--------|:-----------:|:----------:|:------------:|:---------------:|:----------:|:-------------:|
| `StatBar` | — | ✅ Header | — | — | — | — |
| `DiceDisplay` | — | Used inline | Used inline | — | — | — |
| `RoomMap` | — | ✅ Left | — | — | — | — |
| `CombatLog` | — | — | ✅ Center | — | — | — |
| `InventoryGrid` | — | — | — | ✅ Right | — | — |
| `ActionBar` | — | ✅ Footer | ✅ Footer | — | — | — |

## Visual Theme Constants

```
Primary accent:     #c678dd (purple — arcane/dark fantasy)
Secondary accent:   #e5c07b (gold — loot/treasure)
Danger:             #e06c75 (red — HP loss, death, warnings)
Success:            #98c379 (green — healing, gains, safe)
Neutral:            #abb2bf (text)
Dim:                #5c6370 (disabled, explored, secondary info)

HP bar:             Green → Yellow → Red gradient by percentage
Silver:             Gold text (bold)
Points:             Purple text
Monster HP bar:     Red
Room (current):     Bright border, cyan highlight
Room (explored):    Dim border
Room (unexplored):  "?" symbol
Door (available):   Bright arrow
Door (dead end):    "✗" symbol
```

## Responsive Minimums

| Screen | Min Width | Min Height | Degradation |
|--------|:---------:|:----------:|-------------|
| TitleScreen | 60 | 15 | Title text wraps |
| GameScreen | 80 | 20 | RoomMap hidden if <70 wide; RoomInfo full width |
| CombatScreen | 60 | 18 | CombatLog truncated to 8 lines |
| CharacterScreen | 70 | 20 | Inventory scrolls if too many items |
| ShopScreen | 50 | 16 | PriceTable scrolls |
| LevelUpScreen | 50 | 14 | Centered, always fits |

## Theme Registration

All screens apply via `CSS_PATH = "path/to/css"` on the Textual App subclass. Common constants in `src/dark_fort/app.py` as `App.CSS`.
