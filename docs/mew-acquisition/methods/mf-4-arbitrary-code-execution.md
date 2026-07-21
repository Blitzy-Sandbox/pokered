# MF-4: Arbitrary code execution (RAM-as-code)

## Mechanism summary

- What this repository proves is a single legitimate primitive: the item-use routine `UseItem_` reads the current item id, indexes `ItemUsePtrTable`, loads the selected handler address into `hl`, and transfers control with a computed indirect jump `jp hl` `[engine/items/item_effects.asm:L1-16]`; the table it indexes is defined immediately below `[engine/items/item_effects.asm:L17-19]`. This is ordinary table-driven dispatch to a legitimate handler — nothing more.
- The arbitrary code execution (ACE) family — publicly disclosed under names such as **8F**, **ws m**, and **-g m**, along with related item, PC, and text-box redirections — is an *external* body of technique that repurposes this dispatch primitive. **The following characterization is external disclosure metadata drawn from the public corpus, not behavior demonstrated by the cited lines:** the disclosed setups arrange for a computed jump (or an equivalent in-game pointer) to land in player-influenced work RAM and cause the CPU to execute those RAM bytes as instructions, after which the injected code can store any species byte — including Mew's `$15` `[constants/pokemon_constants.asm:L30]` — directly into a party or box slot. The repository is cited here only for the storage fact that `wCurPartySpecies` is a work-RAM field that aliases `wCurItem`/`wCurListMenuItem` at one address `[ram/wram.asm:L1101-1103]`.
- Because every ACE variant named above is already published, this chapter documents the family **only as excluded contrast** and never presents it as a novel method.

## Legal-input sequence

- Per the public corpus (external disclosure metadata), ACE setups are in principle reachable with standard controller inputs on real hardware, because the surfaces they repurpose — item-list contents and on-screen pointers — are populated through ordinary play. The repository citation supports only that item use dispatches through a computed jump to a table-selected handler `[engine/items/item_effects.asm:L1-16]`; it does not itself demonstrate any redirection into RAM.
- This guide deliberately withholds any concrete input recipe, byte sequence, or payload; the table below describes only the *nature* of each setup category, not how to perform it.

| Category | Nature |
| --- | --- |
| Item-list manipulation | *Disclosed metadata:* a crafted item id is made to dispatch toward an unintended address. Repository proves only the table-driven computed jump the technique repurposes `[engine/items/item_effects.asm:L1-16]` |
| Text-box / pointer redirection | *Disclosed metadata:* an in-game pointer is aimed at player-influenced work RAM so its bytes are run as code. Repository proves only that `wCurPartySpecies` is such a RAM field, aliasing `wCurItem` at one address `[ram/wram.asm:L1101-1103]` |
| Battle-buffer seeding | *Disclosed metadata:* player-influenced values in battle RAM such as `wEnemyMon` are executed once the program counter reaches them. Repository proves only that `wEnemyMon` is a battle-data buffer `[ram/wram.asm:L1198]` |

- Concrete ACE payloads are publicly disclosed elsewhere and are out of scope to reproduce here, per R1 and the guide's safety constraint.

## Code-cited mechanism

- What the checkout demonstrates: `UseItem_` reads the current item id, indexes `ItemUsePtrTable`, loads the selected handler pointer into `hl`, and executes a computed indirect jump `jp hl` to that handler `[engine/items/item_effects.asm:L1-16]`. This is the legitimate dispatch primitive and nothing more. *External disclosure metadata:* the disclosed 8F item relies on an out-of-range id reaching an unintended address; that behavior is a property of the public technique, not of the cited lines, which only dispatch to a valid table entry `[engine/items/item_effects.asm:L17-19]`.

```asm
ItemUsePtrTable:
; entries correspond to item ids
	dw ItemUseBall       ; MASTER_BALL
```

- Storage fact: the work-RAM field `wCurPartySpecies` shares one address with `wCurItem` and `wCurListMenuItem` `[ram/wram.asm:L1101-1103]`, so its byte is written during ordinary item and list handling and is therefore player-influenced. This citation establishes storage and aliasing only — not that the field is ever executed.

```asm
wCurPartySpecies::
wCurItem::
```

- Storage fact: the enemy battle buffer `wEnemyMon` is a `battle_struct` region `[ram/wram.asm:L1198]` filled with player-influenced values during a fight. *External disclosure metadata:* the disclosed family treats such a region as a place whose bytes can be interpreted as instructions; the citation itself proves only that the buffer exists as storage.

```asm
wEnemyMon:: battle_struct wEnemyMon
```

- *External disclosure metadata (public corpus, not shown by the cited lines):* once the program counter is redirected into such a region, the player-written bytes execute as instructions and the injected code can store Mew's `$15` `[constants/pokemon_constants.asm:L30]` straight into a party or box slot. This whole redirection-and-execution step is the disclosed technique — which is exactly why ACE is documented here as excluded contrast rather than taught.

## Catch step

- ACE never enters the ball-throw flow at all: instead of passing through `ItemUseBall` `[engine/items/item_effects.asm:L104]` and the catch-rate comparison `[engine/items/item_effects.asm:L300-303]`, it writes the species byte directly into party or box memory.
- See the [Game mechanics reference](../02-game-mechanics-reference.md) for the normal capture path that ACE circumvents.

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.** This is the published 8F / ws m / -g m arbitrary-code-execution family, so it is excluded from the guide's novel-method search under R1 and is documented only as contrast.
- See [Novelty verification](../03-novelty-verification.md) for the disclosed-corpus comparison behind this verdict.

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes (in principle), per the public corpus.** The surfaces ACE repurposes are populated through ordinary controller input (external disclosure metadata); the repository citation supports only the legitimate computed-jump dispatch the technique builds on `[engine/items/item_effects.asm:L1-16]`. On real hardware the disclosed family does not require external tools or memory editing.
- It nonetheless fails R1 as a disclosed technique, and this guide withholds any payload regardless of its input reachability.

## Limitations

- Excluded under R1 because the 8F / ws m / -g m family is already publicly disclosed.
- Documented here only as contrast, to be adjudicated against the disclosed corpus, never as a recommended or novel method.
- Deliberately not reproduced as a recipe: no payload, item-quantity byte codes, or step sequence is given. The only repository-demonstrated fact is the legitimate table-driven computed-jump dispatch `[engine/items/item_effects.asm:L1-16]`; the RAM-as-code surface it is made to reach is external disclosure metadata, not a behavior of the cited lines.
- For the full disclosed-corpus comparison and the reasoning behind this exclusion, see the [Novelty verification](../03-novelty-verification.md) chapter.
