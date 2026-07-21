# Overview and constraints

This chapter is the reader's entry into the technical content of the Mew-acquisition guide for the pret **pokered** disassembly of Pokémon Red/Blue. It states the objective, restates the four binding requirements that govern the whole guide (R1–R4), defines the legal-input vocabulary that fixes the boundary of requirement R2, and records the core facts about Mew as a species. It also sets the guide's honesty contract: what follows is a rigorous, source-cited analysis, not a promise of a guaranteed-novel trick.

Every sentence in this guide that asserts how the game behaves ends with an inline `[path:Lx-Ly]` citation to this checkout of the disassembly, per requirement R3, and the consolidated anchor list lives in the [citation index](citation-index.md). Statements about the guide's own rules and structure — such as the requirement definitions in this chapter — are editorial and carry no code citation.

## Objective

- This guide explains, with exhaustive code grounding, how one could obtain and catch Mew in an unmodified pokered build, and it honestly reports the outcome of that analysis rather than presenting a predetermined trick.
- The substantive value is threefold: (a) a code-cited explanation of why Mew cannot be obtained through normal play, developed in [Conclusion and limitations](04-conclusion-and-limitations.md); (b) a bounded enumeration of the input-reachable mechanism families surfaced by a dated public-corpus search — every family it surfaced that could place species `$15` into a catchable state, one chapter per family under `methods/` — rather than a completeness proof of the open-ended glitch space; and (c) a defensible novelty verdict for each mechanism, adjudicated in [Novelty verification](03-novelty-verification.md).
- Mew's internal species index is `$15` (21 decimal) `[constants/pokemon_constants.asm:L30]`, and its catch rate is 45 `[data/pokemon/base_stats/mew.asm:L7]`.

## The four requirements (R1–R4)

The user's request imposes four binding requirements. They are restated here and referenced by ID throughout the guide, and every candidate mechanism is adjudicated against all four.

| ID | Requirement | What it means for this guide |
|----|-------------|------------------------------|
| R1 | Novelty — the method must be novel and not currently disclosed on the internet. | No technique already published online may be presented as new. The Mew glitch (a.k.a. long-range trainer glitch) and the Trainer-Fly glitch are explicitly named as excluded, and they are illustrative of a broader disclosed corpus — the Ditto/special-stat tricks, arbitrary code execution (ACE), save/box corruption, and the Old Man / Cinnabar name-buffer encounter — all catalogued and excluded in [Novelty verification](03-novelty-verification.md). |
| R2 | Inputs only — catch Mew using only inputs provided by the game. | The procedure must be reachable using standard in-game controller inputs alone. Source edits, ROM patches, cheat devices, memory pokes, save-file surgery, and non-standard peripherals are all disallowed; the exact boundary is fixed in the next section. |
| R3 | Mandatory citations — every assertion cites a code file and line number. | Every sentence asserting game behavior ends with an inline `[path:Lx-Ly]` citation to this disassembly. Wikis, forums, and speedrun references may inform the R1 disclosed-corpus baseline but are never cited as evidence of how the game behaves. |
| R4 | Completeness — if multiple methods are found, list every single one. | The guide enumerates all ten candidate mechanism families (MF-1 … MF-10) surfaced by a dated, bounded public-corpus search and adjudicates each against R1 and R2; none is left as "pending" or "to be discovered." The set is exactly those the search surfaced, not a claim of internet-wide completeness. |

The two glitches named in R1 are illustrative, not the full exclusion set: R1 excludes the entire already-disclosed corpus, of which those two are only the best-known members.

## Legal-input vocabulary (R2 boundary)

Requirement R2 restricts the guide to "legitimate game input." To make that boundary unambiguous for every later adjudication, the permitted and excluded input classes are fixed here.

**Permitted:**

- The D-pad (up, down, left, right).
- The face and system buttons: A, B, Start, and Select.
- Menu navigation — opening menus, moving the cursor, and confirming or cancelling selections.
- Name and text entry through the in-game naming and text-input screens.
- In-battle actions: Fight, Item, Pkmn, and Run.
- Timing — that is, when the player presses inputs, down to frame-level precision.
- Saving the game, and soft or hard resetting the console.
- The Game Link Cable used through the normal trade and link-battle flow.

**Excluded:**

- External RAM or ROM editing of any kind.
- Cheat cartridges such as the Game Genie or GameShark.
- Emulator memory pokes or state manipulation outside normal play.
- Save-file editing performed outside the game.
- Any peripheral or accessory used outside its normal in-game function.

Timing is admitted as a legal input because it only perturbs the random number generator's seed: the generator draws its entropy from the hardware divider register `rDIV`, which it reads directly `[engine/math/random.asm:L3]`, so varying when a button is pressed changes the seed but never injects new data into the game.

The Game Link Cable is admitted with a provenance limit. A trade relays only a Pokémon that a cartridge already holds, so *receiving* an externally-sourced or event Mew by trade is not a qualifying catch or origination for this guide. A method qualifies only when legitimate inputs on an unmodified game *originate* Mew; relaying a pre-existing one merely defers its provenance to whatever produced it, which is why the link-trade path is adjudicated as transfer-only in [MF-6](methods/mf-6-link-trade.md).

## Mew facts (from source)

Mew is defined in the disassembly as a species. The facts a capture attempt depends on are drawn directly from source below.

| Fact | Value | Source |
|------|-------|--------|
| Species index | `$15` (21) | `[constants/pokemon_constants.asm:L30]` |
| Catch rate | 45 | `[data/pokemon/base_stats/mew.asm:L7]` |
| Type | Psychic | `[data/pokemon/base_stats/mew.asm:L6]` |
| Level-1 learnset | POUND only | `[data/pokemon/base_stats/mew.asm:L13]` |

Taken together, these entries show that Mew is defined as a complete, fully specified species `[data/pokemon/base_stats/mew.asm:L6-13]`; whether it can actually be encountered and caught through legal input is the question chapters `02` through `04` resolve.

## How to read this guide

Read the chapters in order; each builds on the shared mechanics established earlier.

- [Game mechanics reference](02-game-mechanics-reference.md) — the shared primitives (species index, RNG, wild-encounter generation, catch algorithm, RAM buffers, character codec) that every method chapter is evaluated against.
- The ten candidate mechanism families, one chapter each, under `methods/`:
  - [MF-1 — special-stat / interrupted-battle encounter](methods/mf-1-special-stat-encounter.md)
  - [MF-2 — Old Man / Cinnabar name-buffer encounter](methods/mf-2-cinnabar-name-buffer.md)
  - [MF-3 — RNG manipulation of encounters](methods/mf-3-rng-manipulation.md)
  - [MF-4 — arbitrary code execution](methods/mf-4-arbitrary-code-execution.md)
  - [MF-5 — save / box / SRAM corruption](methods/mf-5-save-box-corruption.md)
  - [MF-6 — link-trade state introduction](methods/mf-6-link-trade.md)
  - [MF-7 — move-name-buffer overflow (CoolTrainer♀)](methods/mf-7-move-name-buffer-overflow.md)
  - [MF-8 — remaining HP glitch](methods/mf-8-remaining-hp-glitch.md)
  - [MF-9 — out-of-bounds LOL glitch (oobLG)](methods/mf-9-out-of-bounds-lol-glitch.md)
  - [MF-10 — block out-of-bounds LOL glitch (blockoobLG)](methods/mf-10-blockooblg.md)
- [Novelty verification](03-novelty-verification.md) — the disclosed-corpus baseline and the per-method novelty verdicts.
- [Conclusion and limitations](04-conclusion-and-limitations.md) — the synthesized gap-analysis outcome and the guide's honesty statement.
- [Glossary](glossary.md) — terminology used throughout (species index, Special stat, DV, RNG seed, name buffer, ACE, and more).
- [Citation index](citation-index.md) — the consolidated list of every `path:line` anchor cited in the guide.

Every behavioral claim in every chapter ends with a `[path:Lstart-Lend]` citation that resolves through the [citation index](citation-index.md) to a concrete line in this checkout.
