# MF-4: Arbitrary code execution (RAM-as-code)

## Mechanism summary

- The arbitrary code execution (ACE) family — publicly disclosed under names such as **8F**, **ws m**, and **-g m**, along with related item, PC, and text-box redirections — abuses the game's item-effect dispatch, which selects a code routine from a pointer table indexed by the item id `[engine/items/item_effects.asm:L18]`.
- By steering that dispatch (or an equivalent in-game pointer) into player-controlled work RAM such as `wCurPartySpecies` `[ram/wram.asm:L1101]`, the family makes the CPU execute RAM contents as instructions, after which the running code can store any species byte — including Mew's `$15` `[constants/pokemon_constants.asm:L30]` — directly into a party or box slot.
- Because every ACE variant named above is already published, this chapter documents the family **only as excluded contrast** and never presents it as a novel method.

## Legal-input sequence

- ACE setups are, in principle, reachable with standard controller inputs on real hardware, because the surfaces they abuse — item-list contents and on-screen pointers — are all populated through ordinary play `[engine/items/item_effects.asm:L18]`.
- This guide deliberately withholds any concrete input recipe, byte sequence, or payload; the table below describes only the *nature* of each setup category, not how to perform it.

| Category | Nature |
| --- | --- |
| Item-list manipulation | A crafted item id dispatches through the item-effect pointer table toward an unintended address `[engine/items/item_effects.asm:L18]` |
| Text-box / pointer redirection | An in-game pointer is aimed at player-controlled work RAM so its bytes are run as code `[ram/wram.asm:L1101]` |
| Battle-buffer seeding | Player-influenced values in battle RAM such as `wEnemyMon` become executable once the program counter reaches them `[ram/wram.asm:L1198]` |

- Concrete ACE payloads are publicly disclosed elsewhere and are out of scope to reproduce here, per R1 and the guide's safety constraint.

## Code-cited mechanism

- The item-effect handler jumps to a routine chosen from a pointer table indexed by item id, so an id outside the intended range dispatches to an unintended address — the dispatch behavior the disclosed 8F item abuses to reach attacker-chosen code `[engine/items/item_effects.asm:L18]`.

```asm
ItemUsePtrTable:
; entries correspond to item ids
	dw ItemUseBall       ; MASTER_BALL
```

- Work-RAM fields such as `wCurPartySpecies` are written during ordinary play and even share their address with item and list state, so their contents are player-influenced `[ram/wram.asm:L1101]`.

```asm
wCurPartySpecies::
wCurItem::
```

- The enemy battle buffer `wEnemyMon` is likewise filled with player-influenced values during a fight, making it another region whose bytes can be interpreted as instructions `[ram/wram.asm:L1198]`.

```asm
wEnemyMon:: battle_struct wEnemyMon
```

- Once the program counter is redirected into any such region, those player-written bytes execute as instructions, and the resulting code can store Mew's `$15` `[constants/pokemon_constants.asm:L30]` straight into a party or box slot — which is exactly why ACE is disclosed and excluded here rather than taught.

## Catch step

- ACE never enters the ball-throw flow at all: instead of passing through `ItemUseBall` `[engine/items/item_effects.asm:L104]` and the catch-rate comparison `[engine/items/item_effects.asm:L300-303]`, it writes the species byte directly into party or box memory.
- See the [Game mechanics reference](../02-game-mechanics-reference.md) for the normal capture path that ACE circumvents.

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.** This is the published 8F / ws m / -g m arbitrary-code-execution family, so it is excluded from the guide's novel-method search under R1 and is documented only as contrast.
- See [Novelty verification](../03-novelty-verification.md) for the disclosed-corpus comparison behind this verdict.

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes (in principle).** The surfaces ACE abuses are populated through ordinary controller input `[engine/items/item_effects.asm:L18]`, so on real hardware the family does not require external tools or memory editing.
- It nonetheless fails R1 as a disclosed technique, and this guide withholds any payload regardless of its input reachability.

## Limitations

- Excluded under R1 because the 8F / ws m / -g m family is already publicly disclosed.
- Documented here only as contrast, to be adjudicated against the disclosed corpus, never as a recommended or novel method.
- Deliberately not reproduced as a recipe: no payload, item-quantity byte codes, or step sequence is given — only the dispatch and RAM-as-code surface `[engine/items/item_effects.asm:L18]`.
- For the full disclosed-corpus comparison and the reasoning behind this exclusion, see the [Novelty verification](../03-novelty-verification.md) chapter.
