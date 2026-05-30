# Feature Specification: Dark Fort RPG MVP

**Feature Branch**: `001-dark-fort-rpg`

**Created**: 2026-05-30

**Status**: Draft

**Input**: User description: "Create RPG game MVP based on DARK_FORT.md rules."

## User Scenarios & Testing

### User Story 1 - Start a New Game (Priority: P1)

As a player, I want to start a new game with the default character Kargunt, roll for starting equipment, and enter the first room so I can begin my dungeon delve.

**Why this priority**: Without character creation and entry to the first room, the game cannot be played at all. This is the minimum viable entry point.

**Independent Test**: A new game can be started, character stats displayed with starting equipment, and the entrance room generated with one of four possible outcomes. The player can then interact with the entrance room (fight monster, collect item, receive scroll, or see empty room).

**Acceptance Scenarios**:

1. **Given** the game is launched, **When** the player starts a new game, **Then** they are presented with a character named Kargunt having 15 HP, Silver = 15 + random d6, and starting equipment determined by 1d4 weapon and 1d4 item rolls.
2. **Given** a new game has started, **When** the player enters the entrance room, **Then** a room with d4 doors is generated with content determined by 1d4 roll (item, weak monster, scroll, or quiet).
3. **Given** the entrance room contains a weak monster, **When** the player engages, **Then** combat proceeds according to DARK FORT combat rules (d6 vs monster points, weapon damage, armor absorption).
4. **Given** the character reaches 0 HP during entrance room combat, **When** the player dies, **Then** the game ends with a game over message and option to restart.

---

### User Story 2 - Explore Rooms and Fight Monsters (Priority: P1)

As a player, I want to explore new rooms, encounter monsters, run combat, manage inventory, and track my health and silver so I can progress through the dungeon.

**Why this priority**: Room exploration and combat are the core gameplay loop. Without them, the game is just a character sheet.

**Independent Test**: From a post-entrance state, the player can choose a door, generate a new room (shape, doors, content from room table 1d6), resolve the encounter (combat, trap, soothsayer, void peddler, or nothing), collect rewards/loot, and continue exploring. Combat with both weak and tough monsters works correctly including flee mechanics.

**Acceptance Scenarios**:

1. **Given** the player is in a room with at least one unexplored door, **When** they choose a door to go through, **Then** a new room is generated with a random shape (2d6), d4 doors, and content from the room table (1d6: nothing, pit trap, soothsayer, weak monster, tough monster, void peddler).
2. **Given** the player encounters a weak or tough monster, **When** they choose to attack, **Then** the player rolls d6 against the monster's points; on success weapon damage is dealt to the monster; on failure the player takes damage (reduced by d4 if armor equipped).
3. **Given** the player flees from combat, **When** they choose to flee, **Then** they take d4 damage and the room remains unexplored (can be re-entered later with 1-in-4 chance of weak monster).
4. **Given** the player has armor equipped, **When** they take damage in combat, **Then** a d4 is rolled per hit and subtracted from incoming damage.
5. **Given** the player defeats a monster, **When** it dies, **Then** the monster's points are added to the player's total and loot is rolled per the monster's loot table.
6. **Given** the player re-enters a previously explored or fled-from room, **When** they enter, **Then** there is a 1-in-4 chance a weak monster has taken residence.

---

### User Story 3 - Use Items, Scrolls, and Shop (Priority: P2)

As a player, I want to use potions to heal, activate scrolls for special effects, equip/unequip weapons and armor, and buy items from the Void Peddler using silver.

**Why this priority**: Items, scrolls, and the shop add depth to gameplay but are not required for the basic explore-fight-survive loop. Players can technically play without them (though it's much harder).

**Independent Test**: The player can use a healing potion (restore d6 HP), activate any of the 4 scroll types (summon daemon, palms open, aegis of sorrow, false omen), equip different weapons affecting attack and damage rolls, and visit a Void Peddler room to purchase items with silver at listed prices. Inventory correctly reflects equipped and unequipped state.

**Acceptance Scenarios**:

1. **Given** the player has a potion in inventory, **When** they use it, **Then** d6 HP is restored (not exceeding max HP) and the potion is removed from inventory.
2. **Given** the player has the "Summon Weak Daemon" scroll, **When** they activate it, **Then** the daemon helps for d4 future fights dealing d4 damage per fight, and the scroll is consumed.
3. **Given** the player has the "Palms Open the Southern Gate" scroll (d4 uses), **When** they activate it in combat, **Then** it deals d6+1 damage and one use is expended.
4. **Given** the player has the "Aegis of Sorrow" scroll (d4 uses), **When** they activate it, **Then** incoming damage is reduced by d4 per attack for the remainder of combat, consuming one use.
5. **Given** the player has the "False Omen" scroll, **When** they activate it, **Then** they may either choose the result of the next room table roll or reroll any single die.
6. **Given** the player has the Cloak of Invisibility equipped (d4 charges total), **When** they enter a room with monsters, **Then** they may expend one charge to avoid the fight while still collecting the monster points; cloak is consumed after all charges used.
7. **Given** the player encounters a Void Peddler, **When** they enter the shop, **Then** they can purchase any item from the price list using silver, and silver is deducted accordingly.
8. **Given** the player has silver but a full or unlimited inventory, **When** they buy an item from the Void Peddler, **Then** the item is added to inventory.

---

### User Story 4 - Level Up and Win (Priority: P2)

As a player, I want to level up when I meet requirements (12 rooms explored + 15 points OR 40 silver donated) and receive randomized benefits so I feel progression toward beating the game.

**Why this priority**: Leveling and winning provide long-term motivation but are secondary to the core explore-fight loop. A game is still playable without reaching level-up.

**Independent Test**: The player explores 12 rooms and accumulates 15+ points (or collects 40 silver and "donates" it). On level-up, a benefit is rolled (1d6 from the level-up table) and applied. When all 6 benefits are earned, the game displays a congratulations screen. The two level conditions work independently.

**Acceptance Scenarios**:

1. **Given** the player has explored 12 rooms (including entrance) AND has 15+ points, **When** these conditions are met, **Then** the player levels up: 15 points are deducted from the total (room count continues accumulating), and 1d6 is rolled for a benefit from the unscratched level-up table.
2. **Given** the player has collected 40+ silver, **When** they choose to give 40 silver to the poor of ruined Wästland, **Then** exactly 40 silver is deducted (remaining silver kept) and the player levels up, rolling 1d6 for a benefit.
3. **Given** the "Knighted" benefit is rolled (1), **When** applied, **Then** the player gains the title Sir/Lady Kargunt and the result is scratched off the level-up table.
4. **Given** the "Max HP becomes 20" benefit is rolled (3), **When** applied, **Then** the player's maximum HP increases to 20 and current HP is unaffected.
5. **Given** all 6 level-up benefits have been earned, **When** the player retires, **Then** a congratulations screen appears and the game is complete.
6. **Given** the "Halved damage" benefit is rolled (6), **When** applied, **Then** the player chooses one specific Weak monster type and one specific Tough monster type; only those two types deal half damage permanently, and the result is scratched off.

---

### User Story 5 - Death, Traps, and Special Monster Effects (Priority: P3)

As a player, I want to experience traps, deadly monster abilities, permadeath, and special combat outcomes to capture the dangerous and unforgiving spirit of the Dark Fort.

**Why this priority**: These mechanics add tension and stakes but are polish-level details that enhance the core loop. The MVP is playable without all special effects working perfectly.

**Independent Test**: Pit traps trigger d6 rolls (1-3 take d6 damage, rope gives +1). Necro-Sorcerer death ray fires every other attack for d6 damage and has 1-in-6 chance of transforming player into a maggot (game over). Medusa has 1-in-6 chance of petrification (game over). Death at 0 HP ends the game. Riddling Soothsayer: odd = gain 10 silver or 3 points, even = take d4 damage.

**Acceptance Scenarios**:

1. **Given** the player enters a pit trap room, **When** rolling d6 and getting 1-3, **Then** they take d6 damage; if rope is in inventory, +1 is added to the roll (making 2-3 safe instead of 1-3 dangerous).
2. **Given** the player fights a Necro-Sorcerer, **When** the sorcerer makes any attack (normal or death-ray every other), **Then** the sorcerer's damage alternates between d4 and d6 death-ray; a separate 1-in-6 roll on EVERY attack determines if the player is transformed into a maggot (instant game over).
3. **Given** the player fights a Medusa, **When** the Medusa makes any attack, **Then** a 1-in-6 roll determines petrification with instant game over.
4. **Given** the player has 0 HP, **When** this occurs, **Then** the game ends immediately showing a death screen and option to restart.
5. **Given** the player encounters a Riddling Soothsayer, **When** rolling odd on d6, **Then** the player chooses to gain 10 silver or 3 points; when rolling even, the player takes d4 damage ignoring armor.
6. **Given** the player kills a Ruin Basilisk, **When** the 2-in-6 check succeeds, **Then** the player immediately levels up.
7. **Given** the player kills a Small Stone Troll, **When** awarding points, **Then** the troll grants 7 points instead of its combat rating of 5.

---

### Edge Cases

- What happens when the player enters a room with no doors (dead end)? The player can only go back the way they came — no new rooms can be explored from this room.
- What happens if the player's silver drops below 0? Silver cannot go below 0; purchases are denied if insufficient silver.
- What happens when the player unequips armor mid-combat? The armor's damage reduction no longer applies for subsequent hits in that combat.
- How does the Cloak of Invisibility interact with the Soothsayer or traps (non-monster room content)? The cloak only avoids monster fights — traps, soothsayers, and void peddlers still trigger normally.
- What happens when all level-up benefits are scratched off but the player has not yet "retired"? The player continues until they choose to retire; the game congratulates and ends.
- What happens on re-entering the entrance room? Follow the re-entry rule: 1-in-4 chance of a weak monster.
- How are "d6 rooms explored" counted? Each unique room entered (including entrance) counts as 1; re-entering the same room does not increase the count.
- Can the player use a potion or scroll during combat? Potions: yes, as an action. Scrolls: yes, following each scroll's activation rules.
- What happens when the game is saved mid-combat and reloaded? Full combat state is restored: monster HP, death-ray alternation counter, active scroll effect remaining uses/duration, cloak charge count.

## Requirements

### Functional Requirements

- **FR-001**: System MUST create a new character named Kargunt with 15 HP, Silver = 15 + d6, and starting equipment determined by rolling 1d4 on both the Weapon and Item tables from DARK FORT rules.
- **FR-002**: System MUST generate the Entrance Room with shape from the 2d6 room shape table, d4 doors, and one of four random outcomes (item, weak monster, scroll, quiet) per the Entrance Room table.
- **FR-003**: System MUST generate new rooms upon door selection with random shape (2d6 table), random number of doors (d4 table), and random content (d6 room table).
- **FR-004**: System MUST implement the dice-based combat system: player rolls d6 to hit, comparing against monster's points; on success, weapon damage is dealt (d4-1 if unarmed); on failure, player takes monster damage (reduced by d4 rolled per hit if armor equipped).
- **FR-005**: System MUST support combat flee: player takes d4 damage, room remains unexplored, re-entry has 1-in-4 chance of weak monster.
- **FR-006**: System MUST implement all 4 Weak Monster types with their stats, damage, HP, and loot tables (Skeleton, Cultist, Goblin, Undead Hound).
- **FR-007**: System MUST implement all 4 Tough Monster types with their stats, special abilities, damage, HP, and loot tables (Necro-Sorcerer, Stone Troll, Medusa, Basilisk).
- **FR-008**: System MUST handle monster deaths: slain monsters grant their points; loot is rolled per monster table; Small Stone Troll awards 7 points on kill.
- **FR-009**: System MUST support inventory management: player can carry potions, scrolls, rope, cloak; equip/unequip weapons and armor; use consumable items.
- **FR-010**: System MUST implement all 4 scroll types with their effects and usage limits (Summon Weak Daemon, Palms Open the Southern Gate, Aegis of Sorrow, False Omen).
- **FR-011**: System MUST implement the Void Peddler shop: display all items with prices; allow purchase if player has sufficient silver; deduct silver on purchase.
- **FR-012**: System MUST implement the level-up system: check two conditions (12 rooms + 15 points OR 40+ silver to donate); on rooms+points path: deduct 15 points from total (room count persists); on silver path: deduct exactly 40 silver (remaining silver kept); roll 1d6 for benefit from unscratched table; apply benefit; scratch off result; congratulate on completing all 6.
- **FR-013**: System MUST implement pit traps (d6 roll: 1-3 take d6 damage, +1 if rope) and the Riddling Soothsayer (odd: gain 10 silver or 3 points; even: take d4 damage ignoring armor).
- **FR-014**: System MUST implement monster special abilities: Necro-Sorcerer death-ray (every other attack deals d6 instead of d4) + 1-in-6 maggot transformation on EVERY attack (normal and death-ray); Medusa 1-in-6 petrification on EVERY attack; Basilisk 2-in-6 immediate level-up on kill.
- **FR-015**: System MUST implement death at 0 HP: game ends, death screen displayed, option to restart.
- **FR-016**: System MUST track explored room count (unique rooms only), total points from slain monsters, current/max HP, silver balance, inventory, active scroll effects, and level-up table state.
- **FR-017**: System MUST NOT allow entering a new room through a door that has no unexplored path (e.g., from a dead-end room, only backtracking is allowed).
- **FR-018**: System MUST apply the Cloak of Invisibility effect: when equipped, player may avoid up to d4 fights while collecting monster points from avoided encounters; cloak is consumed after all d4 charges are used.

### Key Entities

- **Character (Kargunt)**: HP (current/max), Silver, Points, explored room count, equipped weapon, equipped armor, inventory items, active effects, level-up table state (which benefits remain unscratched).
- **Room**: Shape (from 2d6 table), number of doors (from d4 table), exploration status (unexplored/explored/fled), content type (from room table), generated monster/trap/loot.
- **Monster**: Type (Weak/Tough), name, points (hit threshold), damage expression (e.g., d4, d6+1), HP, special abilities, loot table (dice + item), death effects (e.g., Stone Troll 7 pts).
- **Weapon**: Name, damage dice, attack modifier, value in silver at Void Peddler.
- **Scroll**: Name, effect description, number of uses (d4 for most, single-use for daemon), effect mechanics.
- **Item**: Name, type (potion/rope/cloak/armor), effect, value at Void Peddler.
- **Game State**: All persistence-relevant data needed to save/load the game: character state, room graph, explored rooms, active combats.

## Clarifications

### Session 2026-05-30

- Q: After leveling via rooms+points path, what happens to points and room count? → A: Deduct 15 points from total; room count continues accumulating (points consumed, rooms permanent)
- Q: When does Necro-Sorcerer's 1-in-6 maggot transformation trigger? → A: On every sorcerer attack (normal d4 and death-ray d6), not only death-ray attacks
- Q: When does Medusa's 1-in-6 petrification trigger? → A: On every Medusa attack
- Q: What happens to silver on the silver level-up path? → A: Deduct exactly 40 silver; any remaining silver is kept (not a full reset to 0)
- Q: Should unarmed damage (d4-1 per DARK FORT) be a formal requirement? → A: Yes; added to FR-004
- Q: Does armor absorb d4 damage per hit or once per combat? → A: Per hit — roll d4 each time the player takes damage
- Q: Does the Cloak of Invisibility have d4 charges total (consumed) or recharge per room? → A: d4 charges total; cloak is consumed after all charges used
- Q: Should the entrance room use the 2d6 room shape table or have its own shape rule? → A: Use the 2d6 shape table for entrance room (same as all rooms)
- Q: Should save system preserve mid-combat state or only save between rooms? → A: Preserve full mid-combat state (monster HP, death-ray counter, active effects, charges)

## Success Criteria

### Measurable Outcomes

- **SC-001**: A player can start a new game and reach their first combat encounter within 30 seconds of launching the game.
- **SC-002**: All combat outcomes (hit, miss, damage calculation, flee) compute correctly against the DARK FORT rules table for all 8 monster types across 100 automated simulated combats.
- **SC-003**: A player can complete a full game run (start → explore 12+ rooms → level up multiple times → retire) in under 30 minutes.
- **SC-004**: The game state survives an application restart without data loss, including mid-combat state (monster HP, death-ray alternation, active scroll effects, cloak charges).
- **SC-005**: A new player with no prior knowledge of DARK FORT can successfully navigate through at least 3 rooms without external help or rulebook reference, based on in-game UI guidance.
- **SC-006**: All dice-based random outcomes (monster spawns, room contents, combat rolls, loot, level-up benefits) match the probability distributions specified in DARK FORT rules within 5% tolerance over 10,000 samples.

## Assumptions

- The game will be playable in a web browser as the primary platform for MVP accessibility.
- Game state will persist locally (browser storage) so a session can be resumed after closing.
- The visual map will draw rooms automatically based on generated room shapes and door positions; players do not draw rooms by hand as in the pen-and-paper original.
- Dice rolls are automated and displayed with animated results; players do not need physical dice.
- The MVP focuses on a single player experience only (no multiplayer, no leaderboards).
- The original DARK FORT rules are the sole source of truth for all game mechanics; no custom house rules or balance changes are introduced in the MVP.
- All random number generation uses a cryptographically sufficient pseudo-random algorithm (adequate for gaming, not cryptographic security).
- "Donating 40 silver to the poor of ruined Wästland" will be an explicit action option available at any time from the character screen (not tied to a specific room or NPC).
- The level-up table has exactly 6 benefits; once all are earned, the player must explicitly choose to retire (the game does not force retirement immediately).
- Starting silver is 15 + d6, rolled once at character creation and displayed as the initial balance.
