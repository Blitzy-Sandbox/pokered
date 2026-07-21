# Catching Mew: a code-grounded guide

This guide investigates, with exhaustive citations to the pret **pokered** disassembly of Pokémon Red/Blue, how a player could obtain and catch **Mew** — internal species index `$15` `[constants/pokemon_constants.asm:L30]` and catch rate 45 `[data/pokemon/base_stats/mew.asm:L7]` — using only legitimate in-game inputs, and reports the honest outcome of that investigation.

## Honesty and limitations

> **This guide presents no fabricated "guaranteed-novel" method.** The known glitch space for obtaining Mew is already publicly disclosed, and novelty is assessed against that public corpus at the time of authoring. The guide's value is a rigorous, code-cited analysis — not a secret trick or clickbait.

- The central finding is that Mew is **fully implemented yet placed in no obtainable location**: every path to species `$15` that uses only legitimate inputs turns out to be table-bounded (an unmodified encounter table can never select it), reachable only through dead debug code `[engine/debug/debug_party.asm:L15-24]`, or a member of the already-disclosed glitch corpus.
- No candidate is presented as new unless it is both input-reachable and absent from the public corpus; where no such candidate exists, the guide says so plainly rather than inventing one.
- The complete, code-cited reasoning behind this conclusion is in [04 - Conclusion and limitations](04-conclusion-and-limitations.md).

## The four requirements

This guide is governed by four requirements, summarized here and detailed in [01 - Overview and constraints](01-overview-and-constraints.md):

- **R1 — Novelty:** the method must not be one already published online. The Mew glitch (a.k.a. the long-range Trainer glitch) and the Trainer-Fly glitch are explicitly excluded, along with the rest of the disclosed corpus.
- **R2 — Inputs only:** the method must be reachable using only legitimate in-game inputs (D-pad, A/B/Start/Select, menu navigation, name/text entry, timing, save/reset, and the Game Link cable through normal play). No ROM/RAM editing, cheat cartridges, or emulator memory pokes.
- **R3 — Citations:** every claim about game behavior carries an inline `[path:Lx-Ly]` citation to this disassembly.
- **R4 — Completeness:** every candidate mechanism family reachable by input is enumerated and adjudicated; none is left as "pending" or "to be discovered."

## How to read the citations

- A citation of the form `[path:Lstart-Lend]` — or `[path:Lx]` for a single line — points to a file and line range in **this** pokered checkout. For example, `[constants/pokemon_constants.asm:L30]` marks the line that defines Mew's species index.
- Source `[path:line]` anchors are the sole authority for how the game behaves; the consolidated list of every anchor used across the guide is in the [Citation index](citation-index.md).
- Where wiki, forum, or speedrun links appear — only within the novelty analysis — they are evidence of **public disclosure** for R1, never proof of game behavior.

## Contents

- [01 - Overview and constraints](01-overview-and-constraints.md)
- [02 - Game mechanics reference](02-game-mechanics-reference.md)
- Methods:
  - [MF-1 - Special-stat / interrupted-battle encounter](methods/mf-1-special-stat-encounter.md)
  - [MF-2 - Old Man / Cinnabar name-buffer](methods/mf-2-cinnabar-name-buffer.md)
  - [MF-3 - RNG manipulation](methods/mf-3-rng-manipulation.md)
  - [MF-4 - Arbitrary code execution](methods/mf-4-arbitrary-code-execution.md)
  - [MF-5 - Save / box / SRAM corruption](methods/mf-5-save-box-corruption.md)
  - [MF-6 - Link trade](methods/mf-6-link-trade.md)
- [03 - Novelty verification](03-novelty-verification.md)
- [04 - Conclusion and limitations](04-conclusion-and-limitations.md)
- [Glossary](glossary.md)
- [Citation index](citation-index.md)

## Scope and ground rules

- This is a **documentation-only** deliverable: the game source is never modified and the byte-exact ROM output is unchanged.
- The only in-ROM code that grants Mew is `DebugNewGameParty` `[engine/debug/debug_party.asm:L15-24]`, unreferenced dead code in the retail builds; it is documented as an exclusion, not offered as a method.
- Source `[path:line]` anchors in this disassembly are the sole authority for every behavioral claim; external references establish only public disclosure for R1.
- Every candidate is adjudicated against both R1 (novelty) and R2 (inputs only); the per-candidate verdicts are in [03 - Novelty verification](03-novelty-verification.md).
