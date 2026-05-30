# Combat Requirements Quality Checklist: Dark Fort RPG MVP

**Purpose**: Validate combat-related requirements for completeness, clarity, consistency, and testability
**Created**: 2026-05-30
**Feature**: [spec.md](../spec.md)

## Requirement Completeness

- [ ] CHK001 — Are hit roll mechanics fully specified: what die (d6), what target value (monster points), what constitutes a tie (tie = hit)? [Completeness, Spec §FR-004]
- [ ] CHK002 — Are damage calculations specified for both success (weapon damage dice) and failure (monster damage dice) outcomes? [Completeness, Spec §FR-004]
- [ ] CHK003 — Is unarmed damage (d4-1) explicitly included as a requirement, and is the scenario where a player loses or sells their weapon addressed? [Completeness, Spec §FR-004]
- [ ] CHK004 — Are attack modifier sources (+1 from Dagger/Sword, +1 from level-up benefit 2, +0 from other weapons) all documented in requirements? [Completeness, Spec §FR-004, §FR-012]
- [ ] CHK005 — Are armor damage reduction mechanics fully specified: per-hit d4 roll, interaction with Aegis of Sorrow scroll, interaction with halved damage benefit? [Completeness, Spec §FR-004, §FR-010, §FR-012]
- [ ] CHK006 — Are flee mechanics fully specified: d4 damage on flee, room status after flee (unexplored, monster persists), re-entry 1-in-4 weak monster on every re-entry? [Completeness, Spec §FR-005]
- [ ] CHK007 — Are all 4 weak monster combat stats (points, damage, HP) enumerated in requirements? [Completeness, Spec §FR-006, Reference Tables]
- [ ] CHK008 — Are all 4 tough monster combat stats (points, damage, HP, special abilities) enumerated in requirements? [Completeness, Spec §FR-007, Reference Tables]
- [ ] CHK009 — Are loot mechanics for all 8 monster types specified: probability (2-in-6 for weak, 1-in-6 or 2-in-6 for tough), item type, silver amounts? [Completeness, Spec §FR-008, Reference Tables]
- [ ] CHK010 — Is the Small Stone Troll's "5 pts, 7 if killed" points rule documented in both requirements and the reference table? [Completeness, Spec §FR-008]
- [ ] CHK011 — Are Necro-Sorcerer death-ray alternation rules fully specified: alternating between d4 and d6 every other attack, reset behavior on combat interruption (save/load, flee/return)? [Completeness, Spec §FR-014]
- [ ] CHK012 — Are scroll combat effects (Palms Open: d6+1 damage, Aegis of Sorrow: -d4 per hit, Summon Daemon: d4 damage) specified with their damage dice and usage limits? [Completeness, Spec §FR-010]
- [ ] CHK013 — Are potion-in-combat rules specified: usable as an action, heals d6 HP, capped at max HP? [Completeness, Spec §FR-009, US3]

## Requirement Clarity

- [ ] CHK014 — Is "roll d6 to hit, tie or surpass monster's points" unambiguous — does a tie count as a hit? [Clarity, Spec §FR-004]
- [ ] CHK015 — Is the phrase "on failure: you are hit" explicit that the damage taken equals the monster's damage dice expression (e.g., d4, d6, d6+1)? [Clarity, Spec §FR-004]
- [ ] CHK016 — Is armor absorption specified per-hit rather than once per combat? [Clarity, Spec §FR-004]
- [ ] CHK017 — Is the Necro-Sorcerer maggot transformation (1-in-6) trigger timing clear — every attack, not only death-ray attacks? [Clarity, Spec §FR-014]
- [ ] CHK018 — Is the Medusa petrification (1-in-6) trigger timing clear — every attack? [Clarity, Spec §FR-014]
- [ ] CHK019 — Is the Basilisk level-up (2-in-6) trigger timing clear — on kill? [Clarity, Spec §FR-014]
- [ ] CHK020 — Is the Riddling Soothsayer odd/even determination clear — a separate d6 roll, not the room content d6? [Clarity, Spec §FR-013]
- [ ] CHK021 — Is pit trap rope interaction clear — rope provides passive +1 to the d6 roll, changing the danger threshold from 1-3 to 1-2? [Clarity, Spec §FR-013]
- [ ] CHK022 — Is the instant-death game-over (maggot/petrified) distinct from 0 HP death — does it supersede combat resolution? [Clarity, Spec §FR-014 vs §FR-015]

## Requirement Consistency

- [ ] CHK023 — Are combat rules consistent between US2 acceptance scenarios and FR-004 (hit roll, damage, armor behavior)? [Consistency, Spec §US2 vs §FR-004]
- [ ] CHK024 — Are weapon damage expressions (d4, d6, d6+1, d6+2) consistent across Starting Weapons table, All Weapons table, and Void Peddler Prices table? [Consistency, Spec §Reference Tables]
- [ ] CHK025 — Are monster stat blocks consistent between Weak/Tough Monster reference tables and FR-006/FR-007? [Consistency, Spec §Reference Tables vs §FR-006, §FR-007]
- [ ] CHK026 — Are "d4-1 if unarmed" and "+1 attack" (from weapons) applied to the same hit roll — does attack modifier stack with weapon-specific bonus? [Consistency, Spec §FR-004]
- [ ] CHK027 — Is the halved-damage level-up benefit (benefit 6) interaction with armor (-d4) and Aegis of Sorrow (-d4) defined — which reduction applies first? [Consistency, Spec §FR-012, §FR-004, §FR-010]

## Scenario Coverage

- [ ] CHK028 — Are requirements defined for the combat turn flow: player attacks → monster attacks → repeat, with explicit order (who goes first)? [Gap, Spec §US2]
- [ ] CHK029 — Are requirements defined for the scenario where the player and monster both reach 0 HP simultaneously (mutual kill)? [Gap, Spec §FR-004, §FR-015]
- [ ] CHK030 — Are requirements defined for mid-combat potion use when the player is at full HP — is the potion wasted or prevented? [Gap, Spec §US3.1]
- [ ] CHK031 — Are requirements defined for flee when the player has 4 or fewer HP — flee deals d4 damage, could kill the player mid-flee? [Gap, Spec §FR-005]
- [ ] CHK032 — Are requirements defined for the Medusa petrify + simultaneous kill scenario — if Medusa is killed on the same round it petrifies the player, what outcome? [Gap, Spec §FR-014]
- [ ] CHK033 — Are requirements defined for Basilisk immediate level-up when all 6 benefits already scratched off? [Gap, Spec §FR-014, §FR-012]
- [ ] CHK034 — Are requirements defined for Necro-Sorcerer death-ray alternation state persistence across save/load and flee/return? [Gap, Spec §FR-014, §SC-004]
- [ ] CHK035 — Are requirements defined for Summon Weak Daemon scroll — does the daemon's d4 damage apply in addition to or instead of the player's weapon attack? [Gap, Spec §FR-010]
- [ ] CHK036 — Are requirements defined for the Aegis of Sorrow scroll — does -d4 reduction stack with armor -d4, and does it expire after combat or at session end? [Gap, Spec §FR-010]

## Edge Case Coverage

- [ ] CHK037 — Are the combat math edge cases defined: minimum damage (can damage be reduced below 0?), unarmed + attack bonus interaction (can unarmed rolls benefit from +1 attack from level-up benefit 2)? [Gap, Spec §FR-004]

## Acceptance Criteria Quality

- [ ] CHK038 — Is SC-002 ("all combat outcomes compute correctly for all 8 monster types across 100 simulated combats") measurable — what defines "correctly"? [Measurability, Spec §SC-002]
- [ ] CHK039 — Can the combat hit probability distributions be objectively verified against the DARK FORT rules? [Measurability, Spec §SC-006]
- [ ] CHK040 — Are the acceptance scenarios for combat-specific user stories (US2, US5) complete enough to derive test cases without consulting DARK_FORT.md? [Acceptance Criteria, Spec §US2, §US5]

## Notes

- Focus: Combat requirements quality across hit mechanics, damage, armor, flee, special abilities, and edge cases.
- Items marked [Gap] identify combat scenarios with no corresponding requirement in the spec.
- Items marked [Clarity] identify spec text that could be interpreted multiple ways.
- Items marked [Consistency] identify potential conflicts between spec sections.
- Deep audit covers all 8 monster types, combat flow, special abilities, and combat-adjacent mechanics (traps, soothsayer).
