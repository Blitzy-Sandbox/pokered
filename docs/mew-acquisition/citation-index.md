# Citation index

This index lists every source anchor cited across the Mew-acquisition guide, grouped by top-level source directory, so that any behavioral claim in any chapter resolves to a real `path:line` in this checkout of the pret **pokered** disassembly. It is the single source of truth for traceability required by **R3**: a reviewer can confirm that each guide assertion maps to a concrete line of code.

Chapters are referenced by short tags: `README`, `01` (overview and constraints), `02` (game-mechanics reference), `03` (novelty verification), `04` (conclusion and limitations), `glossary`, and `mf-1` … `mf-10` (the per-method chapters). Only `path:line` anchors appear here; external corpus URLs (wikis, forums, speedrun references) live solely in `03-novelty-verification.md` as disclosed-corpus metadata and are never used as evidence for game behavior.

## constants/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `constants/battle_constants.asm:L42-44` | Defines `BATTLE_TYPE_NORMAL` (0), `BATTLE_TYPE_OLD_MAN` (1), and `BATTLE_TYPE_SAFARI` (2). | 02, glossary, mf-2 |
| `constants/charmap.asm:L1` | Bytes `$00`–`$17` are `TX_*` text-control codes, not typeable name glyphs. | 02, 03, 04, glossary, mf-2, mf-9, mf-10 |
| `constants/charmap.asm:L12` | The `"@"` string terminator maps to byte `$50`. | 02, glossary, mf-2 |
| `constants/charmap.asm:L63` | The space character maps to byte `$7f`. | 02, 03, glossary, mf-2 |
| `constants/charmap.asm:L92` | The letter `A` maps to byte `$80`. | 02, glossary, mf-2 |
| `constants/charmap.asm:L92-117` | Uppercase `A`–`Z` occupy the contiguous high-byte glyph range `$80`–`$99`, so no typeable name character maps to a low byte such as `$15`. | mf-2 |
| `constants/charmap.asm:L117` | The letter `Z` maps to byte `$99`. | 02, glossary, mf-2 |
| `constants/charmap.asm:L126` | The letter `a` maps to byte `$a0`. | glossary, mf-2 |
| `constants/charmap.asm:L187` | The digit `0` maps to byte `$f6`. | glossary, mf-2 |
| `constants/player_constants.asm:L1-20` | `NUM_PLAYER_NAMES` (3) and the build-specific preset player-name strings selectable at name entry. | mf-2 |
| `constants/pokemon_constants.asm:L30` | `const MEW` assigns Mew the internal species index `$15` (21 decimal). | README, 01, 02, 03, 04, glossary, mf-1, mf-2, mf-3, mf-4, mf-5, mf-6, mf-7, mf-8, mf-9, mf-10 |
| `constants/pokemon_constants.asm:L97` | `const DRATINI` assigns Dratini the internal species index `$58`. | mf-10 |
| `constants/pokemon_constants.asm:L99` | `const KABUTO` assigns Kabuto the internal species index `$5A`. | mf-10 |
| `constants/pokemon_data_constants.asm:L58-60` | `PARTY_LENGTH` (6) and `MONS_PER_BOX` (20) — the party-full and box-full thresholds tested before a ball is thrown. | 02 |
| `constants/pokemon_data_constants.asm:L97-98` | `NUM_WILDMONS EQU 10` — every map's wild table holds exactly ten slots. | mf-3 |
| `constants/serial_constants.asm:L25` | `LINK_STATE_BATTLING EQU $04` marks the in-link-battle state. | 02, glossary, mf-6 |
| `constants/text_constants.asm:L1` | `PLAYER_NAME_LENGTH EQU 8` — a typed player name occupies at most `PLAYER_NAME_LENGTH - 1` (7) characters plus the terminator. | 02, mf-2 |
| `constants/text_constants.asm:L3` | `NAME_LENGTH EQU 11` sets the player-name buffer length. | 02, glossary, mf-2 |
| `constants/text_constants.asm:L8` | `DEF NAME_BUFFER_LENGTH EQU 20` — the 20-byte name/move staging buffer length. | 03, mf-7 |
| `constants/trainer_constants.asm:L1` | `DEF OPP_ID_OFFSET EQU 200` — a species byte below 200 is a wild Pokémon; 200 and above is a trainer class. | mf-1, mf-9, mf-10 |

## data/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `data/events/prizes.asm:L9-42` | The two Game Corner prize-Pokémon lists (`PrizeMenuMon1Entries`/`PrizeMenuMon2Entries`); none of the prize species is Mew. | 04 |
| `data/events/trades.asm:L18-27` | The ten fixed NPC `TradeMons` entries; none of them is Mew. | 04, mf-6 |
| `data/player/names_list.asm:L3-9` | `DefaultNamesPlayerList` — the `"NEW NAME"` entry and the `NUM_PLAYER_NAMES` preset player names; deterministic ROM data, none of which is a species byte. | 02, 03, 04, glossary, mf-2 |
| `data/pokemon/base_stats/mew.asm:L1` | `db DEX_MEW` — Mew has a complete base-stats entry, i.e. it is fully implemented. | 04 |
| `data/pokemon/base_stats/mew.asm:L6` | Mew's type is `PSYCHIC`. | 01 |
| `data/pokemon/base_stats/mew.asm:L6-13` | Mew's base-stats body from its `PSYCHIC` type (L6) through its level-1 `POUND`-only learnset (L13). | 01 |
| `data/pokemon/base_stats/mew.asm:L7` | `db 45 ; catch rate` — Mew's catch rate is 45. | README, 01, 02, 03, 04, glossary, mf-1, mf-2, mf-3, mf-6, mf-10 |
| `data/pokemon/base_stats/mew.asm:L13` | Level-1 learnset `db POUND, NO_MOVE, NO_MOVE, NO_MOVE`. | 01 |
| `data/pokemon/mew.asm:L1-9` | In-source note that Mew's pics and base data are grouped separately because it was a last-minute addition; records the late-addition chronology. | 04 |
| `data/pokemon/names.asm:L23` | `dname "MEW"` — Mew has a defined name. | 04 |
| `data/pokemon/palettes.asm:L154` | `db PAL_MEWMON ; MEW` — Mew has a defined palette. | 04 |
| `data/wild/probabilities.asm:L4-9` | The `wild_chance` MACRO emits each slot's cumulative threshold (`db wild_chance_total - 1`) and its `*2` byte offset (`db wild_chance_slot * 2`). | mf-2 |
| `data/wild/probabilities.asm:L11-28` | `WildMonEncounterSlotChances` — the 10-slot encounter-chance table; the ten `wild_chance` entries sum to 256 (`ASSERT wild_chance_total == 256`). | 02, glossary, mf-3 |

## engine/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `engine/battle/core.asm:L2024-2029` | In-source comment: the Old Man tutorial temporarily saves the player name in `wLinkEnemyTrainerName` (== `wGrassRate`), and a Cinnabar/Route 21 oversight leaves it in the wild buffer (the MissingNo. glitch). | mf-2 |
| `engine/battle/core.asm:L2030-2033` | Old Man tutorial setup copies `wPlayerName` (hl, source) into `wLinkEnemyTrainerName` (de, destination) via `CopyData`, seeding the wild-encounter buffer with name bytes. | 02, 03, mf-2 |
| `engine/battle/core.asm:L3309` | `IsGhostBattle` determines whether the current battle is against an unidentified ghost. | 02 |
| `engine/battle/core.asm:L6138-6141` | Enemy level and stats are copied (`1 + NUM_STATS * 2` bytes) into the `wEnemyMonUnmodified*` mirror via `CopyData`. | mf-1 |
| `engine/battle/core.asm:L6543-6548` | `BattleRandom` uses the shared PRNG only when `wLinkState == LINK_STATE_BATTLING`; otherwise it falls through via `jp nz, Random`. | 02, glossary, mf-1, mf-6 |
| `engine/battle/core.asm:L6642` | `InitBattle` — the battle-entry point that leads into wild-encounter setup. | 02 |
| `engine/battle/core.asm:L6647-6650` | `InitOpponent` copies `wCurOpponent` into `wCurPartySpecies` and `wEnemyMonSpecies2`. | 03, mf-1, mf-9, mf-10 |
| `engine/battle/core.asm:L6664` | `callfar TryDoWildEncounter` triggers wild-encounter generation from the battle-entry path. | 02, mf-1, mf-3 |
| `engine/battle/core.asm:L6674-6676` | Wild-vs-trainer branch: `ld a, [wEnemyMonSpecies2]` / `sub OPP_ID_OFFSET` / `jp c, InitWildBattle` — a species byte below 200 starts a wild battle with that index. | mf-1, mf-9, mf-10 |
| `engine/battle/misc.asm:L1` | In-source comment: `FormatMovesString` "formats a string at wMovesString that lists the moves at wMoves". | mf-7 |
| `engine/battle/misc.asm:L2` | `FormatMovesString:` — the routine that builds the concatenated move-name list. | 03, mf-7 |
| `engine/battle/misc.asm:L4` | `ld de, wMovesString` sets the copy destination to the move-name string buffer. | mf-7 |
| `engine/battle/misc.asm:L16` | `call GetName` stages the current move's name into `wNameBuffer`. | mf-7 |
| `engine/battle/misc.asm:L17-24` | `.copyNameLoop` copies `wNameBuffer` → `de` (`wMovesString`) byte-by-byte until the `'@'` terminator, with no length cap of its own (the unbounded stage). | 03, glossary, mf-7 |
| `engine/battle/wild_encounters.asm:L3-102` | `TryDoWildEncounter` selects the wild species strictly from the current map's tables. | 02 |
| `engine/battle/wild_encounters.asm:L47-53` | Encounter-rate check — the map's `wGrassRate` is compared against `hRandomAdd` to decide whether an encounter occurs. | 02, mf-3 |
| `engine/battle/wild_encounters.asm:L54-65` | Encounter-slot selection compares `hRandomSub` against the cumulative `WildMonEncounterSlotChances` table. | 02, glossary, mf-3 |
| `engine/battle/wild_encounters.asm:L65-66` | `ld c, [hl]` loads the selected slot's `*2` byte offset, then `ld hl, wGrassMons` points at the wild-species table. | mf-2 |
| `engine/battle/wild_encounters.asm:L66-80` | The species is read from `wGrassMons`/`wWaterMons` into `wCurPartySpecies`/`wEnemyMonSpecies2`. | 02, mf-2, mf-3 |
| `engine/battle/wild_encounters.asm:L71-72` | Comment: a Cinnabar east-coast "left shore" half-block loads grass encounters. | mf-2 |
| `engine/battle/wild_encounters.asm:L74-80` | The wild species is loaded from the table into `wCurEnemyLevel`/`wCurPartySpecies`/`wEnemyMonSpecies2`; no arithmetic can reach a species absent from the table (the bounding fact). | 02, 03, 04, mf-3 |
| `engine/battle/wild_encounters.asm:L75` | `add hl, bc` indexes `wGrassMons` by the selected slot's byte offset. | mf-2 |
| `engine/battle/wild_encounters.asm:L75-78` | `add hl, bc` / `ld a, [hli]` → `wCurEnemyLevel`, then `ld a, [hl]` reads the species at the next byte. | 03, 04, mf-2 |
| `engine/battle/wild_encounters.asm:L76-80` | The level and species bytes are read from the indexed `wGrassMons` slot into `wCurEnemyLevel` and the species buffers. | mf-2 |
| `engine/debug/debug_party.asm:L1` | `SetDebugNewGameParty` is "unreferenced except in `_DEBUG`". | 04, README, glossary |
| `engine/debug/debug_party.asm:L15` | `DebugNewGameParty:` — the label of the debug party-list data table (itself annotated "unreferenced except in `_DEBUG`"). | 03 |
| `engine/debug/debug_party.asm:L15-24` | `DebugNewGameParty` assembles the party-grant bytes `db MEW, 5` (`_DEBUG`) / `db MEW, 20` (retail); the retail byte is present in the ROM but its reader is unreferenced except `_DEBUG` (see the L1 anchor). This range establishes only that these bytes exist and are dead in retail; the *uniqueness* claim — that they are the sole `MEW`-into-party grant in the ROM — rests on the repository-wide `MEW`-symbol audit in [04](04-conclusion-and-limitations.md), not on this range alone. | 04, README, glossary |
| `engine/debug/debug_party.asm:L24` | The `ELSE`-branch grant byte `db MEW, 20`, assembled into the retail build but never reached in normal play. | 03 |
| `engine/debug/debug_party.asm:L34` | `PrepareNewGameDebug` is "dummy except in `_DEBUG`". | 04 |
| `engine/debug/debug_party.asm:L158-159` | In a retail build `PrepareNewGameDebug` compiles to a bare `ret` (the `ELSE` branch), so the debug party list is never read in normal play. | 04 |
| `engine/items/item_effects.asm:L1-16` | `UseItem_` computes an effect-handler address from item data and jumps to it via `jp hl`. | 03 |
| `engine/items/item_effects.asm:L17-19` | `ItemUsePtrTable` — the item-effect dispatch table mapping each item id to its effect routine (e.g. `ItemUseBall`). | mf-4 |
| `engine/items/item_effects.asm:L104` | `ItemUseBall` — the entry point for throwing a Poké Ball. | 02, mf-1, mf-2, mf-3, mf-4, mf-5, mf-6, mf-7, mf-8, mf-9, mf-10 |
| `engine/items/item_effects.asm:L106-109` | Out-of-battle guard: balls cannot be used when not in battle. | 02 |
| `engine/items/item_effects.asm:L107-109` | Out-of-battle guard body: `ld a, [wIsInBattle]` / `and a` / `jp z, ItemUseNotTime`. | 02 |
| `engine/items/item_effects.asm:L109` | `jp z, ItemUseNotTime` aborts ball use when not in a battle. | 02 |
| `engine/items/item_effects.asm:L111-113` | Trainer-mon guard: balls cannot catch a trainer's Pokémon. | 02 |
| `engine/items/item_effects.asm:L113` | `jp nz, ThrowBallAtTrainerMon` diverts the throw when the target is a trainer's Pokémon. | 02 |
| `engine/items/item_effects.asm:L115-118` | Old Man battle-type check (`wBattleType` == 1) branches past the party/box-full test. | 02 |
| `engine/items/item_effects.asm:L115-125` | Old Man battle skips the party/box-full check. | 02 |
| `engine/items/item_effects.asm:L120-122` | Party-full test: `ld a, [wPartyCount]` / `cp PARTY_LENGTH`. | 02 |
| `engine/items/item_effects.asm:L120-125` | Party-and-box-full rejection block guarding the throw. | 02 |
| `engine/items/item_effects.asm:L123-125` | Box-full test: `ld a, [wBoxCount]` / `cp MONS_PER_BOX` → `BoxFullCannotThrowBall`. | 02 |
| `engine/items/item_effects.asm:L149-153` | Ghost-battle branch: `callfar IsGhostBattle` then `jp z` marks the target uncatchable. | 02 |
| `engine/items/item_effects.asm:L152-153` | Ghost branch loads the can't-be-caught value (`$10`) and skips the capture math. | 02 |
| `engine/items/item_effects.asm:L155-157` | Old Man recheck: `ld a, [wBattleType]` / `dec a` / `jr nz, .notOldManBattle`. | 02 |
| `engine/items/item_effects.asm:L155-164` | Old Man branch: after the recheck it performs the guaranteed-capture name copy and jumps to `.captured`. | 02 |
| `engine/items/item_effects.asm:L159-164` | Old Man branch of `ItemUseBall` restores `wPlayerName` from the wild-monster buffer (`hl = wGrassRate` source, `de = wPlayerName` dest); the in-source comment states the reverse. | 02, 03, mf-2 |
| `engine/items/item_effects.asm:L166-175` | `.notOldManBattle` ghost-Marowak guard (`RESTLESS_SOUL` on `POKEMON_TOWER_6F`). | 02 |
| `engine/items/item_effects.asm:L169-175` | Ghost-Marowak guard: `RESTLESS_SOUL` on `POKEMON_TOWER_6F` is uncatchable. | 02, glossary |
| `engine/items/item_effects.asm:L174-175` | Ghost-Marowak path loads the can't-be-caught value (`$10`) and skips capture. | 02 |
| `engine/items/item_effects.asm:L300-303` | Catch-rate comparison: `cp b` then `jr c, .failedToCapture`. | 02, glossary, mf-1, mf-2, mf-3, mf-4, mf-5, mf-6, mf-7, mf-8, mf-9, mf-10 |
| `engine/items/item_effects.asm:L305-308` | Auto-capture branch: when the intermediate value `W > 255` (`hQuotient + 2` nonzero), the ball captures unconditionally (`jr nz, .captured`) — so a catch-rate-255 target is a guaranteed capture. | 02, 03, glossary, mf-8 |
| `engine/items/item_effects.asm:L310-316` | Second random comparison (Rand2): `call Random` then `cp` against `hQuotient + 3` → `jr c, .failedToCapture`. | 02 |
| `engine/items/item_effects.asm:L318-321` | The `.captured` / `.failedToCapture` label region concluding the catch decision. | 02 |
| `engine/items/item_effects.asm:L321` | The `.failedToCapture` label — the miss path of the catch decision. | 02 |
| `engine/items/item_effects.asm:L395-412` | Shake-count determination (0–3 shakes) from the capture quotient. | 02, glossary |
| `engine/items/item_effects.asm:L414-416` | The Poké Ball animation/result is stored to `wPokeBallAnimData` (`.setAnimData`). | 02 |
| `engine/items/item_effects.asm:L2309-2310` | `BoxFullCannotThrowBall` — the abort path taken when both party and box are full. | 02 |
| `engine/link/cable_club.asm:L4` | `CableClub_DoBattleOrTrade` drives the Colosseum/Trade Center link session. | mf-6 |
| `engine/link/cable_club.asm:L131-135` | The link trade loads the player/enemy party-data blocks and exchanges them via `Serial_ExchangeBytes`. | 03, 04 |
| `engine/link/cable_club.asm:L274-278` | Writes `wLinkState` (`LINK_STATE_TRADING` for a trade, `LINK_STATE_BATTLING` for a battle). | mf-6 |
| `engine/link/cable_club.asm:L682` | `TradeCenter_Trade` — the Trade Center trade-execution routine. | mf-6 |
| `engine/link/cable_club.asm:L800` | `call RemovePokemon` removes the traded-away Pokémon from the player's party. | mf-6 |
| `engine/link/cable_club.asm:L816-817` | `call CopyData` loads the received `wLoadedMon`, then `AddEnemyMonToPlayerParty` adds it — copying an existing partner Pokémon, never fabricating one. | mf-6 |
| `engine/math/random.asm:L1-13` | `Random_` derives a 16-bit value from the hardware divider `rDIV` via the seed bytes `hRandomAdd`/`hRandomSub`. | 02, 03, glossary, mf-3 |
| `engine/math/random.asm:L3` | `ldh a, [rDIV]` — the RNG reads the hardware divider register as its entropy source. | 01, 02, glossary, mf-3 |
| `engine/menus/naming_screen.asm:L243-250` | Name-entry length cap: a typed player name is limited to `PLAYER_NAME_LENGTH - 1` (7) characters. | 02, 03, 04, glossary, mf-2 |
| `engine/menus/save.asm:L11` | `ld a, $2` — the good-checksum `wSaveFileStatus` value. | mf-5 |
| `engine/menus/save.asm:L23` | `ld a, $1` — the bad-checksum `wSaveFileStatus` value. | mf-5 |
| `engine/menus/save.asm:L237-240` | `SaveMainData` writes the checksum computed over the `sGameData` block (`CalcCheckSum` → `sMainDataCheckSum`) to SRAM. | 03 |
| `engine/menus/save.asm:L312` | `CalcIndividualBoxCheckSums` recomputes each PC box's SRAM checksum. | mf-5 |
| `engine/menus/save.asm:L329` | `GetBoxSRAMLocation` maps a box number to its SRAM bank and pointer. | mf-5 |
| `engine/movie/oak_speech/oak_speech2.asm:L70-77` | The chosen player name is copied (`NAME_LENGTH` bytes) into `wNameBuffer` via `CopyData` during Oak's intro. | mf-2 |
| `engine/movie/oak_speech/oak_speech2.asm:L192-214` | `GetDefaultName` walks the name list to the preset entry at index `a` (used when the player picks a default name). | mf-2 |
| `engine/pokemon/bills_pc.asm:L95` | `BillsPC_` — Bill's PC box-storage entry point. | mf-5 |
| `engine/pokemon/bills_pc.asm:L207` | `BillsPCDeposit` reads `wPartyCount` when depositing a Pokémon. | mf-5, mf-8 |
| `engine/pokemon/bills_pc.asm:L256` | `BillsPCWithdraw` reads `wBoxCount` — the complementary withdraw path from Bill's PC. | mf-5, mf-8 |

## home/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `home/copy.asm:L15` | `CopyData::` — the label of the `bc`-length byte-copy routine. | mf-7 |
| `home/copy.asm:L15-16` | `CopyData` copies `bc` bytes from `hl` (source) to `de` (destination). | 02, mf-2 |
| `home/names2.asm:L86-88` | `ld de, wNameBuffer` / `ld bc, NAME_BUFFER_LENGTH` / `call CopyData` — `GetName` stages a fixed 20-byte copy of the move name into `wNameBuffer`. | 03, mf-7 |
| `home/pics.asm:L11-23` | `cp MEW` graphics-bank selection (Mew's pic is in bank `$1`); a loader, not a placement into an obtainable source. | 04 |
| `home/pokemon.asm:L399-400` | `cp MEW` special-cases base-stats loading; a loader, not a placement into an obtainable source. | 04 |
| `home/random.asm:L1-12` | `Random` is the public wrapper (`farcall Random_`) that returns `hRandomAdd`. | 02, mf-3 |
| `home/serial.asm:L1-18` | `Serial` interrupt handler exchanges bytes through `rSB`/`rSC`. | mf-6 |
| `home/serial.asm:L55` | `Serial_ExchangeBytes` — the link byte-exchange routine (invoked for the party-data exchange from `engine/link/cable_club.asm:L131-135`); it relays existing bytes only and so cannot fabricate or transform a species value, and cannot introduce a `MEW` byte that is not already present on the partner cartridge. | mf-6 |
| `home/trainers.asm:L128-159` | `CheckFightingMapTrainers` starts a map trainer's engagement through normal movement. | 03 |
| `home/trainers.asm:L233-235` | `InitBattleEnemyParameters` copies `wEngagedTrainerClass` into `wCurOpponent`. | mf-1 |
| `home/trainers.asm:L327-338` | `EngageMapTrainer` loads the engaged trainer's class and set into `wEngagedTrainerClass` / `wEngagedTrainerSet`. | mf-1 |

## ram/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `ram/hram.asm:L270-271` | `hRandomAdd`/`hRandomSub` — the RNG seed bytes. | 02, glossary, mf-3 |
| `ram/sram.asm:L12-21` | The `SRAM` "Save Data" section declares the persisted save-data layout — `sGameData`/`sPlayerName`/`sMainData`/`sPartyData`/`sCurBoxData` — establishing persistence and region layout only. That this layout can be *corrupted* to inject or reinterpret party/box data is external disclosure metadata, documented as such in [mf-5](methods/mf-5-save-box-corruption.md); it is not a behavior this range establishes. | 03, glossary |
| `ram/sram.asm:L16` | `sGameData` — start of the persisted save-data region. | mf-5 |
| `ram/sram.asm:L18` | `sMainData` — the main saved-game data block. | mf-5 |
| `ram/sram.asm:L20-21` | `sPartyData` and `sCurBoxData` — the persisted party and current-box SRAM blocks. | mf-5 |
| `ram/sram.asm:L21` | `sCurBoxData` — the current PC box's saved data. | mf-5 |
| `ram/sram.asm:L24` | `sMainDataCheckSum` — the checksum byte guarding the main save data. | mf-5 |
| `ram/sram.asm:L33` | `sBox{n}` macro — each PC box's SRAM storage block. | mf-5 |
| `ram/sram.asm:L41` | `sBank2AllBoxesChecksum` — aggregate checksum for the first saved-box bank. | mf-5 |
| `ram/wram.asm:L525-582` | The 39-byte `UNION` that overlays the enemy unmodified-stat buffers with `wEngagedTrainerClass` / `wEngagedTrainerSet`. | mf-1 |
| `ram/wram.asm:L563` | `wEnemyMonUnmodifiedSpecial` — the enemy's unmodified Special-stat word within the union. | mf-1 |
| `ram/wram.asm:L580` | `wEngagedTrainerClass` — the engaged-trainer class byte aliasing the enemy-mon union region. | mf-1 |
| `ram/wram.asm:L899-906` | The 20-byte `UNION` where `wNameBuffer` (name staging) aliases `wMoveData` (in-battle move data). | 03, glossary, mf-7 |
| `ram/wram.asm:L1101` | `wCurPartySpecies` — the current party/encounter species byte. | 02, glossary, mf-4 |
| `ram/wram.asm:L1101-1103` | `wCurPartySpecies`, `wCurItem`, and `wCurListMenuItem` share one WRAM address (a `UNION` alias). | mf-4 |
| `ram/wram.asm:L1193` | `wEnemyMonSpecies2` — the enemy species byte the wild-vs-trainer branch tests. | mf-1 |
| `ram/wram.asm:L1198` | `wEnemyMon` — the enemy Pokémon battle struct. | 02, mf-1, mf-4, mf-7 |
| `ram/wram.asm:L1563` | `wMovesString:: ds NUM_MOVES * MOVE_NAME_LENGTH` — the concatenated move-name string buffer that `FormatMovesString` writes into. | 03, mf-7 |
| `ram/wram.asm:L1715` | `wPlayerName` (`ds NAME_LENGTH`) — the player-name buffer. | 02, glossary, mf-2 |
| `ram/wram.asm:L2144-2155` | The `UNION` where `wGrassRate`/`wGrassMons` alias `wLinkEnemyTrainerName`, so the Old Man name save lands in the wild-encounter buffer. | 02, mf-2 |
| `ram/wram.asm:L2145-2151` | `wGrassRate`/`wGrassMons`/`wWaterRate`/`wWaterMons` — the wild-encounter data buffers. | 02, glossary, mf-2 |

## scripts/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `scripts/ViridianCity.asm:L62-83` | The Old Man catch tutorial sets `BATTLE_TYPE_OLD_MAN` from a normal input path. | 02, 03, glossary, mf-2 |

## text/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `text/PokemonMansion2F.asm:L31-32` | Pokémon Mansion 2F journal flavor ("newly discovered / #MON, MEW.") — story text, not a species placement. | 04 |
| `text/PokemonMansion3F.asm:L33` | Pokémon Mansion 3F journal flavor ("MEW gave birth.") — story text, not a species placement. | 04 |

## build & docs

These anchors support the optional reader-verification appendix only (AAP §0.9); they are build/toolchain references, not evidence for any behavioral capture claim.

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `INSTALL.md:L148` | `make` builds `pokered.gbc` and `pokeblue.gbc`. | 04 |
| `Makefile:L1-4` | The default `roms` target builds `pokered.gbc`, `pokeblue.gbc`, and `pokeblue_debug.gbc`. | 04 |
| `Makefile:L96-97` | `compare:` runs `sha1sum -c roms.sha1` for byte-exact ROM verification. | 04, glossary |
| `Makefile:L104-106` | The `DEBUG=1` flag adds `RGBASMFLAGS += -E` to generate a debug sym/map; it does not define `_DEBUG`. | 04 |
| `Makefile:L111` | The `pokeblue_debug` object is assembled with `-D _DEBUG`, compiling the `_DEBUG`-gated code (e.g. the debug party) into `pokeblue_debug.gbc` only — illustration only. | 04 |
| `.rgbds-version:L1` | The build toolchain is pinned to RGBDS `1.0.1`. | 04 |

## See also

- [Guide home](README.md)
- [Game mechanics reference](02-game-mechanics-reference.md)
