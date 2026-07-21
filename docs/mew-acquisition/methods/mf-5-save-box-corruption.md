# MF-5: Save / box / SRAM corruption

## Mechanism summary

- This chapter covers the **disclosed** save/box/SRAM-corruption family — publicly labelled the **255-Pokémon (box/party overflow) glitch**, **expanded-party / box manipulation**, and **checksum abuse**. What the repository establishes is only that the save system stores the persistent game data in an SRAM region beginning at `sGameData` `[ram/sram.asm:L16]`; the corruption or reinterpretation of that data is the disclosed technique (external disclosure metadata), not a behavior of the cited storage layout.
- The species byte at issue for this guide is `$15`, the internal index that identifies Mew `[constants/pokemon_constants.asm:L30]`; the SRAM layout reserves party and current-box data at `sPartyData` / `sCurBoxData` `[ram/sram.asm:L20-21]`, and a successful disclosed variant would place or reinterpret Mew's byte in one of those slots — the placement being the external technique, not something the layout citation demonstrates.
- The persistent data these variants target is guarded by a checksum that the loader validates on boot, recording a good result as `$2` `[engine/menus/save.asm:L11]` or, when validation fails, a bad result as `$1` `[engine/menus/save.asm:L23]`.
- Because this entire family is already documented publicly, it appears here **only as an EXCLUDED contrast** and is never presented as a novel method.

## Legal-input sequence

- No working corruption recipe is given here — this section only classifies the family's reachability.
- *External disclosure metadata:* the public corpus reports that some corruption is partially reachable through **in-game save-and-reset timing**. What the repository supports is only the persistence fact that the ordinary save flow writes a checksum-validated state which the loader later grades as good (`$2`) `[engine/menus/save.asm:L11]` or bad (`$1`) `[engine/menus/save.asm:L23]`; that a mistimed save/reset yields an exploitable corrupt state is the disclosed claim, not a behavior shown by these lines.
- Editing the save file directly on a PC (or with any external tool) is **not** a game-provided input and is therefore excluded by R2; only in-game save/reset actions count as legal inputs.
- No byte-level payload, address list, or step-by-step sequence is provided, by design.

## Code-cited mechanism

- The save system stores the player's game data — including party and current-box data — in a contiguous SRAM region beginning at `sGameData`, with `sMainData` and `sCurBoxData` laid out within it `[ram/sram.asm:L16]`, `[ram/sram.asm:L18]`, `[ram/sram.asm:L21]`:

```asm
sGameData::
sPlayerName::  ds NAME_LENGTH
sMainData::    ds wMainDataEnd - wMainDataStart
```

- That region is protected by a checksum byte, `sMainDataCheckSum`, stored immediately after the game data `[ram/sram.asm:L24]`.
- The PC boxes are stored separately as `sBox{n}` slots emitted by a macro `[ram/sram.asm:L33]`, and each SRAM bank of boxes carries its own aggregate checksum such as `sBank2AllBoxesChecksum` `[ram/sram.asm:L41]`.
- Per-box checksums are (re)computed by `CalcIndividualBoxCheckSums`, which walks each box in turn `[engine/menus/save.asm:L312]`:

```asm
CalcIndividualBoxCheckSums:
	ld hl, sBox1 ; sBox7
	ld de, sBank2IndividualBoxChecksums ; sBank3IndividualBoxChecksums
```

- A box's physical SRAM address is resolved by `GetBoxSRAMLocation` `[engine/menus/save.asm:L329]`.
- Party-to-box movement is mediated by Bill's PC — the storage engine entered at `BillsPC_` `[engine/pokemon/bills_pc.asm:L95]` — whose deposit path `BillsPCDeposit` reads the party count before moving a Pokémon `[engine/pokemon/bills_pc.asm:L207]`:

```asm
BillsPCDeposit:
	ld a, [wPartyCount]
	dec a
```

- The complementary withdraw path is `BillsPCWithdraw` `[engine/pokemon/bills_pc.asm:L256]`.
- *External disclosure metadata:* the disclosed variants work by **desynchronizing these counts and checksums** so that bytes already resident in the SRAM party/box region are re-graded as valid and reinterpreted as species indices. The repository is cited only for the storage region `sGameData` `[ram/sram.asm:L16]` and for the routine `CalcIndividualBoxCheckSums` that recomputes per-box checksums `[engine/menus/save.asm:L312]`; neither the desynchronization nor the re-grading is a behavior of these normal routines — they are the surface the disclosed technique manipulates.
- Because that desynchronization is the entirety of the published technique, the family is disclosed and excluded, and no working sequence is reproduced here.

## Catch step

- Corruption of this kind places or reinterprets a stored species byte **directly in save/box data**, so it never traverses the normal capture path — it bypasses `ItemUseBall` `[engine/items/item_effects.asm:L104]` and its catch-rate comparison `[engine/items/item_effects.asm:L300-303]` entirely.
- For the normal capture pipeline that this family sidesteps, see the [Game mechanics reference](../02-game-mechanics-reference.md).

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.**
- This family is publicly documented under names including the **255-Pokémon glitch**, **expanded-party manipulation**, and **checksum manipulation**; it is therefore not novel and is excluded by R1.
- The species it would ultimately target, Mew = `$15` `[constants/pokemon_constants.asm:L30]`, does not change the verdict.
- See the [Novelty verification](../03-novelty-verification.md) chapter for the full disclosed-corpus comparison.

## Inputs-only verdict (R2)

- Verdict: **Partially input-reachable.**
- Per the public corpus (external disclosure metadata), some corruption can be induced through in-game save/reset timing; the repository citation supports only that the loader grades a checksum-validated save state as good (`$2`) `[engine/menus/save.asm:L11]` or bad (`$1`) `[engine/menus/save.asm:L23]`. To the extent the disclosed technique is real, that portion does not by itself require external tools — but the corruption mechanism is disclosed, not demonstrated by these lines.
- However, the technique is disclosed and thus already fails R1, and any variant that relies on editing the save file on a PC would additionally violate R2 because that is not a game-provided input.

## Limitations

- This is a **disclosed** family and is excluded by R1; it is documented here as contrast only.
- No working corruption recipe is provided — only the save/SRAM/box surface (for example `sGameData` `[ram/sram.asm:L16]` and `CalcIndividualBoxCheckSums` `[engine/menus/save.asm:L312]`) is described, enough to classify the family.
- Even the portion the disclosed corpus reports as reachable through in-game save/reset timing (whose only repository anchor is the loader's checksum grading `[engine/menus/save.asm:L11]`) remains a disclosed technique, so it cannot satisfy the novelty requirement.
- For the adjudication that places this family in the excluded corpus, see the [Novelty verification](../03-novelty-verification.md) chapter.
