# MF-8: Remaining HP glitch

## Mechanism summary

- The remaining HP glitch — publicly disclosed as the **remaining HP glitch** (a.k.a. **party remaining HP glitch**) — is a box-manipulation technique that reinterprets a party Pokémon's remaining HP as a species index, so it can produce any Generation I index, including Mew's `$15` `[constants/pokemon_constants.asm:L30]`.
- **The following characterization is external disclosure metadata drawn from the public corpus, not behavior demonstrated by the cited lines:** the disclosed procedure first obtains a base Pokémon whose catch rate is 255, reduces it to a chosen remaining-HP value, and then reorders box deposits/withdrawals against a glitch Pokémon so that the stored HP byte is re-read as the party mon's species index. What the repository establishes is only that a catch-rate-255 target is a guaranteed capture, because the Ball's "W > 255" branch captures unconditionally `[engine/items/item_effects.asm:L305-308]`, and that `$15` is Mew's index `[constants/pokemon_constants.asm:L30]`.
- Because the family is already published, this chapter documents it **only as excluded contrast** and never presents it as a novel method.

## Legal-input sequence

- Per the public corpus (external disclosure metadata), the setup is reachable with standard controller inputs on real hardware: it uses ordinary box deposit/withdraw ordering and in-game HP reduction, all driven through menus and battles. The repository citation supports only the storage primitives the technique reorders — Bill's PC deposit reads the party count `[engine/pokemon/bills_pc.asm:L207]` and withdraw reads the box count `[engine/pokemon/bills_pc.asm:L256]`.
- This guide deliberately withholds any concrete recipe, HP value, or deposit ordering; only the *nature* of the mechanism is described.

## Code-cited mechanism

- The one legitimate primitive the technique depends on is the guaranteed capture of a catch-rate-255 base Pokémon. In `ItemUseBall`, after the catch-rate comparison `[engine/items/item_effects.asm:L300-303]`, an intermediate value greater than 255 takes the unconditional capture branch `[engine/items/item_effects.asm:L305-308]`:

```asm
	ldh a, [hQuotient + 2]
	and a
	jr nz, .captured
```

- Bill's PC provides the ordinary deposit and withdraw operations the disclosed procedure reorders: `BillsPCDeposit` reads `wPartyCount` before moving a Pokémon `[engine/pokemon/bills_pc.asm:L207]`, and `BillsPCWithdraw` reads `wBoxCount` `[engine/pokemon/bills_pc.asm:L256]`.
- *External disclosure metadata:* the reinterpretation of a remaining-HP byte as a species index, and the resulting hybrid party mon, are the disclosed technique; neither is a behavior of the storage or capture routines cited above — those routines only move already-owned party/box data and capture a genuinely catchable target.

## Catch step

- The only actual ball throw in this family captures the catch-rate-255 *base* Pokémon through the normal path that begins at `ItemUseBall` `[engine/items/item_effects.asm:L104]`; that capture is guaranteed by the "W > 255" branch `[engine/items/item_effects.asm:L305-308]`.
- Producing species `$15` `[constants/pokemon_constants.asm:L30]` afterward is the disclosed HP-to-species reinterpretation, not a capture — Mew is never the target of a Ball. See the [Game mechanics reference](../02-game-mechanics-reference.md) for the normal capture pipeline.

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.** The remaining HP glitch is in the public corpus, so it is excluded from the guide's novel-method search under R1 and is documented only as contrast.
- Capability note: unlike the bounded families, this technique **can** yield species `$15` `[constants/pokemon_constants.asm:L30]`; it is excluded on novelty grounds (R1), not on capability.
- See [Novelty verification](../03-novelty-verification.md) for the disclosed-corpus comparison behind this verdict.

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes (controller-only, though not ordinary play).** The box-reorder and HP-reduction steps are legitimate game inputs (external disclosure metadata); the repository citations support only the guaranteed-capture primitive `[engine/items/item_effects.asm:L305-308]` and the deposit/withdraw operations `[engine/pokemon/bills_pc.asm:L207]`, `[engine/pokemon/bills_pc.asm:L256]`.
- It nonetheless fails R1 as a disclosed technique, and this guide withholds any concrete sequence regardless of its input reachability.

## Limitations

- Excluded under R1 because the remaining HP glitch is already publicly disclosed.
- It **can** produce Mew, but only through the disclosed HP-to-species reinterpretation — not through a legitimate capture of species `$15` `[constants/pokemon_constants.asm:L30]`.
- Deliberately not reproduced as a recipe: no catch-rate-255 base list, HP value, or deposit ordering is given — only the guaranteed-capture and storage primitives it repurposes `[engine/items/item_effects.asm:L305-308]`, `[engine/pokemon/bills_pc.asm:L207]`.
- For the full disclosed-corpus comparison and the reasoning behind this exclusion, see the [Novelty verification](../03-novelty-verification.md) chapter.
