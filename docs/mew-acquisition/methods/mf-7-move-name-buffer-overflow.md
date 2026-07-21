# MF-7: Move-name-buffer overflow (CoolTrainer♀ / move-0x00 corruption)

## Mechanism summary

- The move-name-buffer overflow family — publicly disclosed under names such as **CoolTrainer♀** and **move-0x00** ("--" glitch-move) corruption — abuses `FormatMovesString`, the routine that builds the on-screen move list `[engine/battle/misc.asm:L1]`.
- For each move id it is given, `FormatMovesString` calls `GetName` to stage that move's name into the fixed 20-byte `wNameBuffer` `[home/names2.asm:L86-88]`, then copies characters out of that buffer until it meets a `"@"` terminator, with no length cap on the copy `[engine/battle/misc.asm:L17-24]`.
- When a move's staged name contains no `"@"` terminator among its bytes, the copy over-reads past `wNameBuffer` and over-writes past the `wMovesString` destination, corrupting the adjacent work RAM `[ram/wram.asm:L899-906]` and the region following the move-string buffer `[ram/wram.asm:L1563]` — the surface the disclosed corruption drives.
- Because both CoolTrainer♀ and move-0x00 corruption are already published, this chapter documents the family **only as excluded contrast** and never presents it as a novel method.

## Legal-input sequence

- Not a clean inputs-only path. The overflow only occurs once the game already holds a glitch move whose internal name lacks a `"@"` terminator; obtaining such a move is itself a product of the disclosed glitch corpus, not of ordinary movement, menu, or name-entry input.
- The routines involved are ordinary game code reached during normal play `[engine/battle/misc.asm:L2]`, but reaching the corrupted state depends on that disclosed precondition, so there is no clean-save, controller-only sequence that leads to species `$15`.
- This guide deliberately withholds any concrete recipe for obtaining the glitch move or triggering the overflow; only the *nature* of the mechanism is described.

## Code-cited mechanism

- `FormatMovesString` sets its destination to `wMovesString` and, for each move in the list, calls `GetName` `[engine/battle/misc.asm:L4]` `[engine/battle/misc.asm:L16]`.
- `GetName` locates the requested move name and stages a fixed `NAME_BUFFER_LENGTH` (20) bytes `[constants/text_constants.asm:L8]` into `wNameBuffer` using `CopyData` `[home/names2.asm:L86-88]` `[home/copy.asm:L15]`.

```asm
	ld de, wNameBuffer
	ld bc, NAME_BUFFER_LENGTH
	call CopyData
```

- `FormatMovesString` then copies characters out of `wNameBuffer` into `wMovesString` until a `"@"`, with no byte counter bounding the loop `[engine/battle/misc.asm:L17-24]`.

```asm
	ld a, [hli]
	cp '@'
	jr z, .doneCopyingName
```

- `wNameBuffer` is only `NAME_BUFFER_LENGTH` (20) bytes and is overlaid — via a `UNION`/`NEXTU` block that the source itself notes "spans 20 bytes" — onto the in-battle move data `wMoveData`, so the two occupy the same WRAM region `[ram/wram.asm:L899-906]`; the write destination `wMovesString` is likewise a fixed region `[ram/wram.asm:L1563]`.

- If the staged name has no `"@"` among its bytes, the copy over-reads past `wNameBuffer` and over-writes past `wMovesString`, so it corrupts whatever WRAM follows those buffers. It does not, by itself, rewrite the enemy-species byte, which lives in a different region `[ram/wram.asm:L1198]`; the corruption's effect depends entirely on what disclosed setup placed in the overrun path.

## Catch step

- This family does not pass through the ball-throw flow: it corrupts RAM through the move-list formatter rather than by entering `ItemUseBall` `[engine/items/item_effects.asm:L104]` and the catch-rate comparison `[engine/items/item_effects.asm:L300-303]`.
- Any resulting species byte is produced by the disclosed corruption, not by a legitimate capture. See the [Game mechanics reference](../02-game-mechanics-reference.md) for the normal capture path this bypasses.

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.** This is the published CoolTrainer♀ / move-0x00 corruption family, so it is excluded from the guide's novel-method search under R1 and is documented only as contrast.
- See [Novelty verification](../03-novelty-verification.md) for the disclosed-corpus comparison behind this verdict.

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes (controller-only, though not ordinary play).** R2 permits *any* legitimate controller input, not merely intended ("ordinary") play, so the distinction matters here: the move-name formatter is ordinary code reached on every battle turn `[engine/battle/misc.asm:L2]`, and the disclosed corpus documents a controller-only route (external disclosure metadata) to the prerequisite state — a move slot holding an unterminated-name glitch move — without any memory editor, cheat device, or save surgery. That the route is not part of *ordinary* play does not make it input-unreachable; it remains reachable with the standard controller alone.
- The verdict is therefore **not** "partially input-reachable": it is input-reachable under R2. It fails the guide's novel-method test only on R1, as a disclosed technique — and this guide withholds any concrete trigger sequence, glitch-move acquisition steps, or byte values regardless of that reachability.

## Limitations

- Excluded under R1 because CoolTrainer♀ / move-0x00 corruption is already publicly disclosed.
- The overflow corrupts the move-name staging buffer and the WRAM adjacent to `wMovesString` `[ram/wram.asm:L899-906]` `[ram/wram.asm:L1563]`; it is not a direct, inputs-only lever that writes species `$15` `[constants/pokemon_constants.asm:L30]` into a catchable, party, or box slot.
- Deliberately not reproduced as a recipe: no glitch-move acquisition steps, byte values, or trigger sequence are given — only the routines and RAM surface involved `[engine/battle/misc.asm:L17-24]`.
- For the full disclosed-corpus comparison and the reasoning behind this exclusion, see the [Novelty verification](../03-novelty-verification.md) chapter.
