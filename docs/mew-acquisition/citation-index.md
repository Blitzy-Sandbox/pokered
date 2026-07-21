# Citation index

This index lists every source anchor cited across the Mew-acquisition guide, grouped by top-level source directory, so that any behavioral claim in any chapter resolves to a real `path:line` in this checkout of the pret **pokered** disassembly. It is the single source of truth for traceability required by **R3**: a reviewer can confirm that each guide assertion maps to a concrete line of code.

Chapters are referenced by short tags: `README`, `01` (overview and constraints), `02` (game-mechanics reference), `03` (novelty verification), `04` (conclusion and limitations), `glossary`, and `mf-1` … `mf-6` (the per-method chapters). Only `path:line` anchors appear here; external corpus URLs (wikis, forums, speedrun references) live solely in `03-novelty-verification.md` as disclosed-corpus metadata and are never used as evidence for game behavior.

## constants/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `constants/battle_constants.asm:L42-44` | Defines `BATTLE_TYPE_NORMAL` (0), `BATTLE_TYPE_OLD_MAN` (1), and `BATTLE_TYPE_SAFARI` (2). | 02, glossary, mf-2 |
| `constants/charmap.asm:L1` | Bytes `$00`–`$17` are `TX_*` text-control codes, not typeable name glyphs. | 02, glossary, mf-2 |
| `constants/charmap.asm:L63` | The space character maps to byte `$7f`. | 02, glossary, mf-2 |
| `constants/charmap.asm:L92` | The letter `A` maps to byte `$80`. | 02, glossary, mf-2 |
| `constants/charmap.asm:L93` | The letter `B` maps to byte `$81`. | mf-2 |
| `constants/charmap.asm:L117` | The letter `Z` maps to byte `$99`. | 02, glossary, mf-2 |
| `constants/charmap.asm:L126` | The letter `a` maps to byte `$a0`. | glossary, mf-2 |
| `constants/charmap.asm:L187` | The digit `0` maps to byte `$f6`. | glossary, mf-2 |
| `constants/pokemon_constants.asm:L30` | `const MEW` assigns Mew the internal species index `$15` (21 decimal). | README, 01, 02, 03, 04, glossary, mf-1, mf-2, mf-3, mf-4, mf-5, mf-6 |
| `constants/pokemon_data_constants.asm:L97-98` | `NUM_WILDMONS EQU 10` — every map's wild table holds exactly ten slots. | mf-3 |
| `constants/serial_constants.asm:L25` | `LINK_STATE_BATTLING EQU $04` marks the in-link-battle state. | 02, glossary, mf-6 |
| `constants/text_constants.asm:L3` | `NAME_LENGTH EQU 11` sets the player-name buffer length. | 02, glossary, mf-2 |
| `constants/trainer_constants.asm:L1` | `DEF OPP_ID_OFFSET EQU 200` — a species byte below 200 is a wild Pokémon; 200 and above is a trainer class. | mf-1 |

## data/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `data/events/trades.asm:L18-27` | The ten fixed NPC `TradeMons` entries; none of them is Mew. | 04, mf-6 |
| `data/pokemon/base_stats/mew.asm:L1` | `db DEX_MEW` — Mew has a complete base-stats entry, i.e. it is fully implemented. | 04 |
| `data/pokemon/base_stats/mew.asm:L6` | Mew's type is `PSYCHIC`. | 01 |
| `data/pokemon/base_stats/mew.asm:L7` | `db 45 ; catch rate` — Mew's catch rate is 45. | README, 01, 02, 03, 04, glossary, mf-1, mf-2, mf-3, mf-6 |
| `data/pokemon/base_stats/mew.asm:L13` | Level-1 learnset `db POUND, NO_MOVE, NO_MOVE, NO_MOVE`. | 01 |
| `data/pokemon/names.asm:L23` | `dname "MEW"` — Mew has a defined name. | 04 |
| `data/pokemon/palettes.asm:L154` | `db PAL_MEWMON ; MEW` — Mew has a defined palette. | 04 |
| `data/wild/probabilities.asm:L11-28` | `WildMonEncounterSlotChances` — the 10-slot encounter-chance table; the ten `wild_chance` entries sum to 256 (`ASSERT wild_chance_total == 256`). | 02, glossary, mf-3 |

## engine/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `engine/battle/core.asm:L2024-2029` | In-source comment: the Old Man tutorial temporarily saves the player name in `wLinkEnemyTrainerName` (== `wGrassRate`), and a Cinnabar/Route 21 oversight leaves it in the wild buffer (the MissingNo. glitch). | mf-2 |
| `engine/battle/core.asm:L2030-2033` | Old Man tutorial setup copies `wPlayerName` (hl, source) into `wLinkEnemyTrainerName` (de, destination) via `CopyData`, seeding the wild-encounter buffer with name bytes. | 02, mf-2 |
| `engine/battle/core.asm:L3309` | `IsGhostBattle` determines whether the current battle is against an unidentified ghost. | 02 |
| `engine/battle/core.asm:L6543-6548` | `BattleRandom` uses the shared PRNG only when `wLinkState == LINK_STATE_BATTLING`; otherwise it falls through via `jp nz, Random`. | 02, glossary, mf-1, mf-6 |
| `engine/battle/core.asm:L6647-6650` | `InitOpponent` copies `wCurOpponent` into `wCurPartySpecies` and `wEnemyMonSpecies2`. | mf-1 |
| `engine/battle/core.asm:L6664` | `callfar TryDoWildEncounter` triggers wild-encounter generation from the battle-entry path. | 02, mf-1, mf-3 |
| `engine/battle/core.asm:L6674-6676` | Wild-vs-trainer branch: `ld a, [wEnemyMonSpecies2]` / `sub OPP_ID_OFFSET` / `jp c, InitWildBattle` — a species byte below 200 starts a wild battle with that index. | mf-1 |
| `engine/battle/wild_encounters.asm:L3-102` | `TryDoWildEncounter` selects the wild species strictly from the current map's tables. | 02 |
| `engine/battle/wild_encounters.asm:L47-53` | Encounter-rate check — the map's `wGrassRate` is compared against `hRandomAdd` to decide whether an encounter occurs. | 02, mf-3 |
| `engine/battle/wild_encounters.asm:L54-65` | Encounter-slot selection compares `hRandomSub` against the cumulative `WildMonEncounterSlotChances` table. | 02, glossary, mf-3 |
| `engine/battle/wild_encounters.asm:L66-80` | The species is read from `wGrassMons`/`wWaterMons` into `wCurPartySpecies`/`wEnemyMonSpecies2`. | 02, mf-2, mf-3 |
| `engine/battle/wild_encounters.asm:L71-72` | Comment: a Cinnabar east-coast "left shore" half-block loads grass encounters. | mf-2 |
| `engine/battle/wild_encounters.asm:L74-80` | The wild species is loaded from the table into `wCurEnemyLevel`/`wCurPartySpecies`/`wEnemyMonSpecies2`; no arithmetic can reach a species absent from the table (the bounding fact). | 02, mf-3 |
| `engine/debug/debug_party.asm:L1` | `SetDebugNewGameParty` is "unreferenced except in `_DEBUG`". | 04 |
| `engine/debug/debug_party.asm:L15-24` | `DebugNewGameParty` lists `db MEW, 5` / `db MEW, 20` — the only in-ROM Mew grant, dead code in retail builds. | 04, glossary |
| `engine/debug/debug_party.asm:L34` | `PrepareNewGameDebug` is "dummy except in `_DEBUG`". | 04 |
| `engine/items/item_effects.asm:L18` | `ItemUsePtrTable` dispatches each item id to its effect routine. | mf-4 |
| `engine/items/item_effects.asm:L104` | `ItemUseBall` — the entry point for throwing a Poké Ball. | 02, mf-1, mf-2, mf-3, mf-4, mf-5, mf-6 |
| `engine/items/item_effects.asm:L106-109` | Out-of-battle guard: balls cannot be used when not in battle. | 02 |
| `engine/items/item_effects.asm:L111-113` | Trainer-mon guard: balls cannot catch a trainer's Pokémon. | 02 |
| `engine/items/item_effects.asm:L115-125` | Old Man battle skips the party/box-full check. | 02 |
| `engine/items/item_effects.asm:L149-153` | Ghost-battle branch: `callfar IsGhostBattle` then `jp z` marks the target uncatchable. | 02 |
| `engine/items/item_effects.asm:L159-164` | Old Man branch of `ItemUseBall` restores `wPlayerName` from the wild-monster buffer (`hl = wGrassRate` source, `de = wPlayerName` dest); the in-source comment states the reverse. | 02, mf-2 |
| `engine/items/item_effects.asm:L169-175` | Ghost-Marowak guard: `RESTLESS_SOUL` on `POKEMON_TOWER_6F` is uncatchable. | 02, glossary |
| `engine/items/item_effects.asm:L300-303` | Catch-rate comparison: `cp b` then `jr c, .failedToCapture`. | 02, glossary, mf-1, mf-2, mf-3, mf-4, mf-5, mf-6 |
| `engine/items/item_effects.asm:L395-412` | Shake-count determination (0–3 shakes) from the capture quotient. | 02, glossary |
| `engine/items/item_effects.asm:L414-416` | The Poké Ball animation/result is stored to `wPokeBallAnimData` (`.setAnimData`). | 02 |
| `engine/link/cable_club.asm:L4` | `CableClub_DoBattleOrTrade` drives the Colosseum/Trade Center link session. | mf-6 |
| `engine/link/cable_club.asm:L274-278` | Writes `wLinkState` (`LINK_STATE_TRADING` for a trade, `LINK_STATE_BATTLING` for a battle). | mf-6 |
| `engine/math/random.asm:L1-13` | `Random_` derives a 16-bit value from the hardware divider `rDIV` via the seed bytes `hRandomAdd`/`hRandomSub`. | 02, glossary, mf-3 |
| `engine/math/random.asm:L3` | `ldh a, [rDIV]` — the RNG reads the hardware divider register as its entropy source. | 02, glossary, mf-3 |
| `engine/menus/save.asm:L11` | `ld a, $2` — the good-checksum `wSaveFileStatus` value. | mf-5 |
| `engine/menus/save.asm:L23` | `ld a, $1` — the bad-checksum `wSaveFileStatus` value. | mf-5 |
| `engine/menus/save.asm:L312` | `CalcIndividualBoxCheckSums` recomputes each PC box's SRAM checksum. | mf-5 |
| `engine/menus/save.asm:L329` | `GetBoxSRAMLocation` maps a box number to its SRAM bank and pointer. | mf-5 |
| `engine/pokemon/bills_pc.asm:L95` | `BillsPC_` — Bill's PC box-storage entry point. | mf-5 |
| `engine/pokemon/bills_pc.asm:L207` | `BillsPCDeposit` reads `wPartyCount` when depositing a Pokémon. | mf-5 |
| `engine/pokemon/bills_pc.asm:L256` | `BillsPCWithdraw` — the complementary withdraw path from Bill's PC. | mf-5 |

## home/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `home/copy.asm:L15-16` | `CopyData` copies `bc` bytes from `hl` (source) to `de` (destination). | 02, mf-2 |
| `home/pics.asm:L11-23` | `cp MEW` graphics-bank selection (Mew's pic is in bank `$1`); a loader, not a placement into an obtainable source. | 04 |
| `home/pokemon.asm:L399-400` | `cp MEW` special-cases base-stats loading; a loader, not a placement into an obtainable source. | 04 |
| `home/random.asm:L1-12` | `Random` is the public wrapper (`farcall Random_`) that returns `hRandomAdd`. | 02, mf-3 |
| `home/serial.asm:L1-18` | `Serial` interrupt handler exchanges bytes through `rSB`/`rSC`. | mf-6 |
| `home/serial.asm:L55` | `Serial_ExchangeBytes` copies existing party bytes; it does not synthesize a species. | mf-6 |

## ram/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `ram/hram.asm:L270-271` | `hRandomAdd`/`hRandomSub` — the RNG seed bytes. | 02, glossary, mf-3 |
| `ram/sram.asm:L12-21` | The `SRAM` "Save Data" section (`sGameData`/`sPlayerName`/`sCurBoxData`) — persists the save file and the Pokémon storage boxes; the disclosed save/box-corruption surface. | glossary |
| `ram/sram.asm:L16` | `sGameData` — start of the persisted save-data region. | mf-5 |
| `ram/sram.asm:L18` | `sMainData` — the main saved-game data block. | mf-5 |
| `ram/sram.asm:L21` | `sCurBoxData` — the current PC box's saved data. | mf-5 |
| `ram/sram.asm:L24` | `sMainDataCheckSum` — the checksum byte guarding the main save data. | mf-5 |
| `ram/sram.asm:L33` | `sBox{n}` macro — each PC box's SRAM storage block. | mf-5 |
| `ram/sram.asm:L41` | `sBank2AllBoxesChecksum` — aggregate checksum for the first saved-box bank. | mf-5 |
| `ram/wram.asm:L1101` | `wCurPartySpecies` — the current party/encounter species byte. | 02, glossary, mf-4 |
| `ram/wram.asm:L1193` | `wEnemyMonSpecies2` — the enemy species byte the wild-vs-trainer branch tests. | mf-1 |
| `ram/wram.asm:L1198` | `wEnemyMon` — the enemy Pokémon battle struct. | 02, mf-1, mf-4 |
| `ram/wram.asm:L1715` | `wPlayerName` (`ds NAME_LENGTH`) — the player-name buffer. | 02, glossary, mf-2 |
| `ram/wram.asm:L2144-2155` | The `UNION` where `wGrassRate`/`wGrassMons` alias `wLinkEnemyTrainerName`, so the Old Man name save lands in the wild-encounter buffer. | 02, mf-2 |
| `ram/wram.asm:L2145-2151` | `wGrassRate`/`wGrassMons`/`wWaterRate`/`wWaterMons` — the wild-encounter data buffers. | 02, glossary, mf-2 |

## scripts/

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `scripts/ViridianCity.asm:L62-83` | The Old Man catch tutorial sets `BATTLE_TYPE_OLD_MAN` from a normal input path. | 02, glossary, mf-2 |

## build & docs

These anchors support the optional reader-verification appendix only (AAP §0.9); they are build/toolchain references, not evidence for any behavioral capture claim.

| Anchor | What it establishes | Used by |
|--------|---------------------|---------|
| `INSTALL.md:L148` | `make` builds `pokered.gbc` and `pokeblue.gbc`. | README |
| `Makefile:L96-97` | `compare:` runs `sha1sum -c roms.sha1` for byte-exact ROM verification. | README, 04 |
| `Makefile:L104-106` | The `DEBUG=1` build flag exposes `_DEBUG`-gated code (illustration only). | 04 |
| `.rgbds-version:L1` | The build toolchain is pinned to RGBDS `1.0.1`. | README |

## See also

- [Guide home](README.md)
- [Game mechanics reference](02-game-mechanics-reference.md)
