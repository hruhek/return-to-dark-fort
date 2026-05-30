# Game Rules Completeness Checklist: Dark Fort RPG MVP

**Purpose**: Deep audit — cross-reference every DARK_FORT.md rule, table, and mechanic against spec requirements for completeness
**Created**: 2026-05-30
**Feature**: [spec.md](../spec.md)

## Requirement Completeness

- [ ] CHK001 — Are all four starting weapons (Warhammer d6, Dagger d4/+1, Sword d6/+1, Flail d6+1) explicitly enumerated with their stats in functional requirements? [Gap, Spec §FR-001]
- [ ] CHK002 — Are all four starting items (Armor -d4, Potion heal d6, Scroll: Summon weak daemon, Cloak of invisibility) explicitly enumerated with effects in functional requirements? [Gap, Spec §FR-001]
- [ ] CHK003 — Is unarmed damage (d4-1 per DARK FORT combat rules) explicitly specified in the combat requirements? [Gap, Spec §FR-004]
- [ ] CHK004 — Are all four Weak Monster types individually listed with their Points, Damage, HP, and loot table (including 2-in-6 probabilities) in requirements? [Completeness, Spec §FR-006]
- [ ] CHK005 — Are all four Tough Monster types individually listed with their Points, Damage, HP, special abilities, and loot tables in requirements? [Completeness, Spec §FR-007]
- [ ] CHK006 — Are the Void Peddler prices for all ten items explicitly defined in requirements (not just referenced as "price list")? [Gap, Spec §FR-011]
- [ ] CHK007 — Are all 11 room shapes from the 2d6 table (Irregular cave, Oval, Cross-shaped, Corridor, Square, Round, Rectangular, Triangular, Skull-shaped) enumerated? [Gap, Spec §FR-003]
- [ ] CHK008 — Are all six room content types from the 1d6 room table (Nothing, Pit trap, Soothsayer, Weak monster, Tough monster, Void Peddler) explicitly listed with their probabilities? [Completeness, Spec §FR-003]
- [ ] CHK009 — Are all six random item types from the Items 1d6 table (Random weapon, Potion, Rope, Random scroll, Armor, Cloak) individually enumerated? [Gap, Spec §FR-003]
- [ ] CHK010 — Is the "random weapon" sub-table mapping defined (which table is used to generate a random weapon — the starting 1d4 weapon table)? [Ambiguity, Spec §FR-003]
- [ ] CHK011 — Are all four scroll types individually listed with their full effect text (Summon daemon: d4 fights/d4 dmg, Palms: d6+1/d4 uses, Aegis: -d4/d4 uses, False Omen: choose room or reroll) in requirements? [Completeness, Spec §FR-010]
- [ ] CHK012 — Is the entrance room "any size or shape" rule from DARK FORT addressed, given the spec assumes auto-generated room shapes? [Conflict, Spec §FR-002 vs DARK_FORT.md Entrance Room]
- [ ] CHK013 — Are all six level-up benefits individually described with their mechanical effects in requirements or acceptance scenarios? (Knighted, +1 attack, Max HP 20, 5 potions, Mighty Zweihänder, Halved damage chooser) [Completeness, Spec §FR-012, US4]
- [ ] CHK014 — Is the "Mighty Zweihänder (d6+2)" weapon stat defined somewhere in requirements since it appears as both a level-up reward and a Void Peddler item? [Gap]
- [ ] CHK015 — Is the door count distribution (0 doors = dead end, 1 door, 2 doors) explicitly specified in requirements, not just implied by the d4 table? [Clarity, Spec §FR-003]
- [ ] CHK016 — Are the two level-up paths (rooms+points AND/OR silver) documented with their mutual independence — can a player level up multiple times from the same trigger type? [Clarity, Spec §FR-012]
- [ ] CHK017 — Is the Small Stone Troll's "7 if killed" points rule (grants 7 points instead of combat rating 5) documented? [Completeness, Spec §FR-008]
- [ ] CHK018 — Is the Necro-Sorcerer's "3d6 silver" loot and Medusa's "d4×d6 silver" loot explicitly specified alongside other loot rules? [Completeness, Spec §FR-007]
- [ ] CHK019 — Is the cloak of invisibility's "d4 fights" usage limit defined (does it recharge, is it consumed after d4 uses, or does it provide d4 charges total)? [Ambiguity, Spec §FR-018, DARK FORT.md Item B.4]
- [ ] CHK020 — Is the "Rope gives +1 to pit trap roll" specified as a passive inventory benefit or must the player actively use it? [Ambiguity, Spec §FR-013]

## Requirement Clarity

- [ ] CHK021 — Is the "on failure: you are hit" combat rule explicit that the monster's damage dice (not a fixed value) determines damage taken by the player? [Clarity, Spec §FR-004]
- [ ] CHK022 — Is "armor absorbs d4 damage" qualified — does the d4 roll happen once per combat or once per hit received? [Ambiguity, Spec §FR-004]
- [ ] CHK023 — Is the Soothsayer's "Odd" / "Even" described as being determined by a d6 roll (per DARK FORT room table) or by any roll? [Ambiguity, Spec §FR-013]
- [ ] CHK024 — Is the "re-entering explored/fled rooms: 1-in-4 chance of Weak monster" rule clear about whether it applies only once or on every re-entry? [Clarity, Spec §FR-005]
- [ ] CHK025 — Are "explored rooms" counting rules explicit: does the entrance count, do fled rooms count, do re-entries increment the counter? [Clarity, Spec §FR-016, Edge Cases]
- [ ] CHK026 — Is the cloak of invisibility's interaction with monster points clear — does "acquiring all monster points" mean full points or reduced points? [Ambiguity, Spec §FR-018]
- [ ] CHK027 — Is the level-up condition "12 rooms + 15 points" clear about whether both must be met simultaneously (AND) or accumulatively? [Clarity, Spec §FR-012]

## Requirement Consistency

- [ ] CHK028 — Do the acceptance scenarios for level-up benefit application consistently describe all six benefits, or are some listed only in FRs? [Consistency, Spec §US4 vs §FR-012]
- [ ] CHK029 — Are "scroll uses" terminology consistent — some scrolls say "d4 uses" (Palms, Aegis), but Summon Daemon scroll is "consumed" on use (implied single-use); does the spec distinguish these? [Consistency, Spec §FR-010]
- [ ] CHK030 — Is the weapon table for starting equipment (1d4) the same table used for "random weapon" generation from the Items 1d6 table? [Consistency, Spec §FR-001 vs §FR-003]

## Scenario Coverage

- [ ] CHK031 — Are requirements defined for the scenario where the player enters a room, flees, and later re-enters — is the re-entry content roll (1-in-4 weak monster) independent of the original room content? [Coverage, Spec §FR-005]
- [ ] CHK032 — Are requirements defined for what happens when the Basilisk's "2-in-6 immediate level up" triggers but all 6 level-up benefits are already scratched off? [Gap, Spec §FR-014]
- [ ] CHK033 — Are requirements defined for the scenario where both level-up conditions trigger simultaneously (12 rooms + 15 points AND 40 silver available)? [Gap, Spec §FR-012]
- [ ] CHK034 — Are requirements defined for using potions when at full HP (can the player waste a potion, or is it prevented)? [Gap, Spec §US3.1]
- [ ] CHK035 — Are requirements defined for the scenario where the player attempts to flee but has 4 or fewer HP (flee deals d4 damage, could kill the player)? [Gap, Spec §FR-005]

## Edge Case Coverage

- [ ] CHK036 — Is the "retire until the 7th Misery" endgame reference from DARK FORT addressed or explicitly declared out of scope? [Gap, Spec §US4]
- [ ] CHK037 — Are requirements defined for what happens when the player kills a monster with a scroll effect (e.g., Palms Open the Southern Gate) — is the kill attributed correctly for point tracking? [Gap, Spec §FR-010]
- [ ] CHK038 — Is the Necro-Sorcerer's "every other attack" alternation reset behavior defined when combat is interrupted (e.g., player uses potion, flees and returns)? [Gap, Spec §FR-014]
- [ ] CHK039 — Are requirements defined for what happens when the Medusa petrifies the player but the player also defeats the Medusa in the same attack round? [Gap, Spec §FR-014]
- [ ] CHK040 — Are requirements defined for how the "Halved damage permanently" level-up benefit interacts with the Aegis of Sorrow scroll (-d4 damage) — which damage reduction applies first? [Gap, Spec §FR-012 §FR-010]

## Non-Functional Requirements

- [ ] CHK041 — Are dice roll animation/timing requirements specified — e.g., is there a minimum or maximum time a roll animation may take before the result is shown? [Gap]
- [ ] CHK042 — Are save/load state requirements defined for mid-combat saves (e.g., active monster state, pending death-ray alternation, scroll effect durations)? [Coverage, Spec §SC-004]
- [ ] CHK043 — Is the randomness quality requirement (SC-006: "within 5% tolerance over 10,000 samples") specified per individual die or for composite outcomes (e.g., 2d6 distribution)? [Clarity, Spec §SC-006]

## Dependencies & Assumptions

- [ ] CHK044 — Is the "game will be playable in a web browser" assumption validated — does any DARK FORT rule assume physical components (drawing rooms, scratching off results) that need explicit digital adaptation requirements? [Assumption, Spec §Assumptions]
- [ ] CHK045 — Is the assumption that "no custom house rules or balance changes" are introduced independently verifiable against every DARK FORT.md rule? [Assumption, Spec §Assumptions]

## Notes

- Items marked [Gap] identify DARK FORT.md rules with no corresponding spec requirement.
- Items marked [Ambiguity] identify spec text that can be interpreted multiple ways under strict DARK FORT reading.
- Items marked [Conflict] identify spec interpretations that may deviate from written DARK FORT rules.
- Deep audit covers all major rule domains: character creation, combat, monsters, rooms, items, scrolls, leveling, and edge cases.
