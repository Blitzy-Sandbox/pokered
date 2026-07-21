# MF-10: Block out-of-bounds LOL glitch (blockoobLG)

## Mechanism summary

- The block out-of-bounds LOL glitch — publicly disclosed as **blockoobLG** — is a variant of [oobLG](mf-9-out-of-bounds-lol-glitch.md) that sets the opponent-species byte through a different source, removing the constraint that keeps plain oobLG out of the control-character index range.
- **External disclosure metadata (from the public corpus, not behavior of the cited lines):** by controlling which item/tile drives the write, blockoobLG can place species indices whose values fall in the control-character range — the disclosed corpus names Dratini and Kabuto as examples that plain oobLG cannot reach. That same control-character range contains Mew's index `$15` `[constants/pokemon_constants.asm:L30]`, `[constants/charmap.asm:L1]`, so blockoobLG **can** set the opponent-species byte to `$15`.
- Because the family is already published, this chapter documents it **only as excluded contrast** and never presents it as a novel method.

## Legal-input sequence

- Per the public corpus (external disclosure metadata), blockoobLG is reachable with standard controller inputs on real hardware, using the same indoor RAM Glitch City family as oobLG with a different controlling source for the written byte. No concrete recipe, item, or Glitch-City route is reproduced here.
- The repository citation supports only the consumption of the resulting species byte, not the out-of-bounds write that produces it.

## Code-cited mechanism

- Once the opponent-species byte is set, `InitOpponent` loads it as the current species and copies it into the enemy-species buffer `[engine/battle/core.asm:L6647-6650]`:

```asm
InitOpponent:
	ld a, [wCurOpponent]
	ld [wCurPartySpecies], a
	ld [wEnemyMonSpecies2], a
```

- The battle setup splits on 200: values below `OPP_ID_OFFSET` `[constants/trainer_constants.asm:L1]` become a wild encounter `[engine/battle/core.asm:L6674-6676]`. Mew's index `$15` (21 decimal) is far below 200, so once the byte is set to `$15` the retail code routes it to a wild battle — exactly the split described in the [Game mechanics reference](../02-game-mechanics-reference.md).
- *External disclosure metadata — the differentiator from oobLG:* blockoobLG's alternate write source lets it place control-character indices (`$00`–`$17` `[constants/charmap.asm:L1]`, which includes `$15`) into the opponent-species byte, whereas plain oobLG cannot. This differential-write behavior is a property of the disclosed setup, not of the cited retail lines, which only read and range-split whatever byte is present.

## Catch step

- If the opponent-species byte is set to `$15`, the resulting wild battle is against Mew, and capture proceeds through the ordinary Ball path at `ItemUseBall` `[engine/items/item_effects.asm:L104]`, using Mew's catch rate of 45 `[data/pokemon/base_stats/mew.asm:L7]` in the catch-rate comparison `[engine/items/item_effects.asm:L300-303]`.
- This is the one bounded/disclosed family that both reaches `$15` `[constants/pokemon_constants.asm:L30]` and terminates in a genuine wild-battle capture through `ItemUseBall` `[engine/items/item_effects.asm:L104]`; it is excluded solely because it is disclosed. See the [Game mechanics reference](../02-game-mechanics-reference.md) for the capture pipeline.

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.** blockoobLG is in the public corpus (documented alongside oobLG) and is included here only as contrast.
- Capability note: blockoobLG **can** yield species `$15` `[constants/pokemon_constants.asm:L30]`; it is excluded on novelty grounds (R1), not on capability.
- See [Novelty verification](../03-novelty-verification.md) for the disclosed-corpus comparison behind this verdict.

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes (controller-only, though not ordinary play).** The Glitch-City setup uses legitimate inputs (external disclosure metadata); the repository citations support only the read `[engine/battle/core.asm:L6647-6650]` and the 200-split `[engine/battle/core.asm:L6674-6676]` of the resulting byte.
- It nonetheless fails R1 as a disclosed technique, and this guide withholds any concrete sequence regardless of its input reachability.

## Limitations

- Excluded under R1 because blockoobLG is already publicly disclosed (as a named oobLG variant).
- It **can** reach species `$15` (via the opponent-species byte the wild-vs-trainer split reads) `[engine/battle/core.asm:L6674-6676]` and terminate in a real capture at the catch-rate comparison `[engine/items/item_effects.asm:L300-303]`, which is precisely why it must be named and excluded rather than overlooked — see the bounding contrast with plain [MF-9: oobLG](mf-9-out-of-bounds-lol-glitch.md).
- Deliberately not reproduced as a recipe; only the read/range-split and capture primitives it feeds are cited `[engine/battle/core.asm:L6647-6650]`, `[engine/battle/core.asm:L6674-6676]`, `[engine/items/item_effects.asm:L300-303]`.
- For the full disclosed-corpus comparison and the reasoning behind this exclusion, see the [Novelty verification](../03-novelty-verification.md) chapter.
