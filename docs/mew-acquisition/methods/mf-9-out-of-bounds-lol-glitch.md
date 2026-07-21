# MF-9: Out-of-bounds LOL glitch (oobLG)

## Mechanism summary

- The out-of-bounds LOL glitch — publicly disclosed as **oobLG** — is a member of the LOL-glitch / unterminated-name family, assembled for "catch-'em-all" speedruns to farm wild Pokémon by writing the opponent-species byte from screen data staged in an indoor RAM Glitch City.
- **External disclosure metadata (from the public corpus, not behavior of the cited lines):** oobLG stages an unterminated-name glitch Pokémon so that the on-screen tile data is copied into the opponent-species byte the battle setup reads. The disclosed technique can set that byte to species indices **below 200**; indices 200 and above are instead treated as Trainers — the same threshold the retail code applies at `[engine/battle/core.asm:L6674-6676]`.
- The repository establishes only how the opponent-species byte is *consumed* once set (read and range-split), never the out-of-bounds write itself. This chapter documents oobLG **only as excluded contrast**.

## Legal-input sequence

- Per the public corpus (external disclosure metadata), oobLG is reachable with standard controller inputs on real hardware: it uses an indoor RAM Glitch City reached through normal movement and menu/PC interaction. No concrete recipe, tile value, or Glitch-City route is reproduced here.
- The repository citation supports only the consumption of the resulting species byte, not its out-of-bounds production.

## Code-cited mechanism

- Once the opponent-species byte is set, `InitOpponent` loads it as the current species and copies it into the enemy-species buffer `[engine/battle/core.asm:L6647-6650]`:

```asm
	ld a, [wCurOpponent]
	ld [wCurPartySpecies], a
	ld [wEnemyMonSpecies2], a
```

- The battle setup then splits on 200: values below `OPP_ID_OFFSET` `[constants/trainer_constants.asm:L1]` are treated as a wild encounter, and values at or above it become Trainers `[engine/battle/core.asm:L6674-6676]`:

```asm
	ld a, [wEnemyMonSpecies2]
	sub OPP_ID_OFFSET
	jp c, InitWildBattle
```

- *External disclosure metadata — the bounding fact that excludes Mew from plain oobLG:* the screen tile oobLG reads is fixed (the disclosed corpus records it as always `7C`), so the opponent-species byte it can write is constrained and does **not** span the control-character range `$00`–`$17`. Mew's index `$15` `[constants/pokemon_constants.asm:L30]` lies inside that control-character range `[constants/charmap.asm:L1]`, so plain oobLG cannot place species `$15` into the opponent-species byte. The `7C`-tile constraint is a property of the disclosed setup, not of the cited retail lines, which only read and range-split whatever byte is present.

## Catch step

- For species indices oobLG *can* produce (below 200, outside the control-character range), the capture proceeds through the ordinary Ball path at `ItemUseBall` `[engine/items/item_effects.asm:L104]` with the standard catch-rate comparison `[engine/items/item_effects.asm:L300-303]`.
- Because oobLG cannot set the opponent-species byte the wild-vs-trainer split reads `[engine/battle/core.asm:L6674-6676]` to `$15` — that value lies in the `$00`–`$17` control-character range `[constants/charmap.asm:L1]` its screen-tile writes never reach — no catch step for Mew exists under this family. See the [Game mechanics reference](../02-game-mechanics-reference.md) for the capture pipeline.

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.** oobLG is in the public corpus and is documented here only as contrast.
- Capability note: even setting novelty aside, plain oobLG **cannot** reach species `$15`, because the byte it writes does not cover the control-character range that contains Mew's index `[constants/charmap.asm:L1]`, `[constants/pokemon_constants.asm:L30]`.
- See [Novelty verification](../03-novelty-verification.md) for the disclosed-corpus comparison behind this verdict.

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes (controller-only, though not ordinary play).** The Glitch-City setup uses legitimate inputs (external disclosure metadata); the repository citations support only the read `[engine/battle/core.asm:L6647-6650]` and the 200-split `[engine/battle/core.asm:L6674-6676]` of the resulting byte.
- Input reachability does not rescue it: it is disclosed (R1) *and* bounded away from `$15` by the `7C`-tile constraint.

## Limitations

- Excluded under R1 as a disclosed technique.
- Additionally bounded: plain oobLG cannot produce species `$15`, because it cannot write a control-character index into the opponent-species byte `[constants/charmap.asm:L1]`. The variant that removes this bound is treated separately in [MF-10: blockoobLG](mf-10-blockooblg.md).
- Deliberately not reproduced as a recipe; only the read/range-split primitives it feeds are cited `[engine/battle/core.asm:L6647-6650]`, `[engine/battle/core.asm:L6674-6676]`.
- For the full disclosed-corpus comparison and the reasoning behind this exclusion, see the [Novelty verification](../03-novelty-verification.md) chapter.
