# Glossary

This glossary defines the terms and acronyms used throughout the Mew-acquisition guide so that each mechanics chapter and method chapter can be read without re-deriving vocabulary. It is referenced by every other file in the set.

How to read the `Source` column: a term that names a concrete ROM symbol or a specific game behavior carries an inline `[path:Lstart-Lend]` citation into this checkout of the pret **pokered** disassembly, satisfying Requirement R3 (every behavioral claim is traceable to a line of code); a general computing concept that the guide uses but that has no single source line is marked `—`. Where a term names a publicly documented glitch, its definition is labeled **DISCLOSED (R1-excluded)** and the term appears only as excluded contrast — never as a novel capture method. The consolidated list of every anchor lives in the [citation index](citation-index.md).

## Game mechanics terms

| Term | Definition | Source |
|------|------------|--------|
| `BATTLE_TYPE_NORMAL` / `BATTLE_TYPE_OLD_MAN` / `BATTLE_TYPE_SAFARI` | The three battle-type constants stored in `wBattleType`: normal (0), the Old Man tutorial (1), and the Safari Zone (2). Only the Old Man tutorial type is set from an ordinary, input-reachable script path. | `[constants/battle_constants.asm:L42-44]` |
| `BattleRandom` | The in-battle RNG wrapper. It uses the shared link-battle PRNG only when `wLinkState` equals `LINK_STATE_BATTLING`; otherwise it falls through to `Random`. | `[engine/battle/core.asm:L6543-6548]` |
| Catch algorithm / shake check | The `ItemUseBall` logic that compares the adjusted random value against the target's catch rate and then derives the number of ball shakes (0–3) from the capture quotient. | `[engine/items/item_effects.asm:L300-303]`, `[engine/items/item_effects.asm:L395-412]` |
| Catch rate | The per-species byte that governs capture probability; a lower value is harder to catch. Mew's catch rate is 45. | `[data/pokemon/base_stats/mew.asm:L7]` |
| charmap (character map) | The text-encoding table. Name-enterable glyphs are HIGH bytes (space `$7f`, `A` = `$80` … `Z` = `$99`, `a` = `$a0`, `0` = `$f6`); the only sub-`$7f` byte a name carries is the `"@"` terminator `$50`, and bytes `$00`–`$17` are text-control codes. Consequently Mew's index byte `$15` is not a typeable name character — though this bounds only the typed bytes, since positions past the terminator or a fixed-length copy hold indeterminate residual, not typed input. | `[constants/charmap.asm:L1]`, `[constants/charmap.asm:L12]`, `[constants/charmap.asm:L63]`, `[constants/charmap.asm:L92]`, `[constants/charmap.asm:L117]`, `[constants/charmap.asm:L126]`, `[constants/charmap.asm:L187]` |
| `DebugNewGameParty` | A data table that lists `db MEW, 5` in a `_DEBUG` build and `db MEW, 20` otherwise, so the grant byte is assembled into the retail ROM as well; it is the only in-ROM grant of Mew. The routine that reads it, `SetDebugNewGameParty`, is unreferenced except in `_DEBUG`, so in retail builds nothing ever copies it into the party and it is not an input-reachable method. | `[engine/debug/debug_party.asm:L15-24]`, `[engine/debug/debug_party.asm:L1]` |
| DV (Determinant Value) | A hidden per-stat value in the range 0–15 that individualizes a Pokémon's stats; a general Generation I concept referenced when the guide discusses stat values. | — |
| Encounter slot | One of the 10 fixed rows in a map's wild table. The active slot is chosen by comparing an RNG-derived index against the cumulative `WildMonEncounterSlotChances` table, so only species already listed in the table can appear. | `[engine/battle/wild_encounters.asm:L54-65]`, `[data/wild/probabilities.asm:L11-28]` |
| Ghost battle / `RESTLESS_SOUL` | The uncatchable-target guard in `ItemUseBall`: on `POKEMON_TOWER_6F`, an enemy species of `RESTLESS_SOUL` (the unidentified ghost Marowak) is flagged as impossible to catch. | `[engine/items/item_effects.asm:L169-175]` |
| `hRandomAdd` / `hRandomSub` | The two HRAM bytes that hold the RNG's running seed and output values. | `[ram/hram.asm:L270-271]` |
| Link battle / `LINK_STATE_BATTLING` | The link-cable connection state (`$04`) that marks an in-progress link battle; it is the value `BattleRandom` tests. Related link states include link trading, whose potential as a Mew source is analyzed in [mf-6](methods/mf-6-link-trade.md). | `[constants/serial_constants.asm:L25]` |
| Mew glitch (long-range trainer glitch) | **DISCLOSED (R1-excluded).** The publicly documented special-encounter technique that yields Mew. It is catalogued only as excluded contrast in [mf-1](methods/mf-1-special-stat-encounter.md) and is never presented as this guide's novel method. | — |
| Missingno. / Cinnabar coast glitch | **DISCLOSED (R1-excluded).** The publicly documented family in which the player-name buffer is read as wild-encounter data on Cinnabar Island's east coast; catalogued only as excluded contrast in [mf-2](methods/mf-2-cinnabar-name-buffer.md). | — |
| Move-name-buffer overflow (move-0x00 / CoolTrainer♀) | **DISCLOSED (R1-excluded).** The publicly documented corruption in which `FormatMovesString` copies a move name out of the fixed 20-byte `wNameBuffer` into `wMovesString` with no length cap on the copy, so a glitch move whose internal name lacks a `"@"` terminator overruns the shared-`UNION` staging RAM and the region past the move-string buffer; catalogued only as excluded contrast in [mf-7](methods/mf-7-move-name-buffer-overflow.md). | `[engine/battle/misc.asm:L17-24]`, `[ram/wram.asm:L899-906]` |
| Name buffer (`wPlayerName`) | The 11-byte player-name storage buffer, sized by `NAME_LENGTH` (11). In the disclosed Cinnabar glitch its bytes are the data reinterpreted as a wild-encounter list. | `[ram/wram.asm:L1715]`, `[constants/text_constants.asm:L3]` |
| Old Man catch tutorial | The Viridian City tutorial battle that sets `BATTLE_TYPE_OLD_MAN` from an ordinary, input-reachable script path. | `[scripts/ViridianCity.asm:L62-83]` |
| `rDIV` | The Game Boy hardware divider register, read by the RNG routine as its entropy source. | `[engine/math/random.asm:L3]` |
| RNG (pseudo-random number generator) | The game's randomness source. The core `Random_` routine derives a 16-bit value from the hardware divider register `rDIV` combined with the `hRandomAdd` / `hRandomSub` seeds. | `[engine/math/random.asm:L1-13]` |
| Special stat / Special-stat trick | The in-battle Special stat. In the **DISCLOSED (R1-excluded)** Mew-glitch family a Special value of 21 is reinterpreted as a species index (21 = Mew); documented only as excluded contrast in [mf-1](methods/mf-1-special-stat-encounter.md). | — |
| Species index number | Each Pokémon's internal one-byte identifier. Mew's is `$15` (21 decimal). | `[constants/pokemon_constants.asm:L30]` |
| Trainer-Fly glitch | **DISCLOSED (R1-excluded).** The interrupted-battle glitch that underlies the Mew glitch; catalogued only as excluded contrast in [mf-1](methods/mf-1-special-stat-encounter.md). | — |
| Wild table (`wGrassMons` / `wWaterMons`) | The WRAM copy of the current map's grass and water encounter lists (each preceded by its encounter rate), from which every wild species is selected. | `[ram/wram.asm:L2145-2151]` |

## Toolchain and reverse-engineering terms

| Term | Definition | Source |
|------|------------|--------|
| ACE (Arbitrary Code Execution) | **DISCLOSED (R1-excluded).** A technique that redirects the CPU program counter into manipulable RAM so that crafted bytes run as code; catalogued only as excluded contrast in [mf-4](methods/mf-4-arbitrary-code-execution.md). | — |
| byte-exact / `make compare` | The build-verification step that confirms the assembled ROM reproduces the original retail image byte for byte, by running `sha1sum -c roms.sha1`. | `[Makefile:L96-97]` |
| HRAM (High RAM) | The Game Boy's fast high-page RAM (`$FF80`–`$FFFE`); in this guide it holds the RNG seed bytes. | `[ram/hram.asm:L270-271]` |
| MBC3 | The cartridge memory-bank controller used by this title; a general hardware concept referenced when discussing memory banking and battery-backed saves. | — |
| pret / pokered / disassembly | The community-maintained source reconstruction that this guide cites: *pret* is the project, *pokered* is this repository, and *disassembly* is its reverse-engineered source form. General project context. | — |
| Save/box corruption (SRAM corruption) | **DISCLOSED (R1-excluded).** The family of techniques that corrupt persistent save and box (SRAM) state to inject or reinterpret Pokémon data; catalogued only as excluded contrast in [mf-5](methods/mf-5-save-box-corruption.md). | — |
| SRAM (Save RAM) | The battery-backed cartridge save RAM that persists the save file and the Pokémon storage boxes. | `[ram/sram.asm:L12-21]` |
| WRAM (Work RAM) | The console's main work RAM, which holds the live party, battle, and wild-encounter buffers. | `[ram/wram.asm:L1101]` |

## See also

- [Guide home](README.md)
- [Game mechanics reference](02-game-mechanics-reference.md)
- [Citation index](citation-index.md)
