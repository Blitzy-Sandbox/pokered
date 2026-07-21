# MF-11: International fossil conversion glitch (wFossilMon → GivePokemon)

## Mechanism summary

- The international fossil conversion glitch — publicly disclosed as the **international fossil conversion glitch**, the English/localized adaptation of the original Japanese **fossil conversion glitch** — reinterprets the stored fossil-revival species so the Pokémon the Cinnabar Island lab scientist hands back becomes a different index, including Mew's `$15` `[constants/pokemon_constants.asm:L30]`.
- **The following characterization is external disclosure metadata drawn from the public corpus, not behavior demonstrated by the cited lines:** the disclosed procedure corrupts the stored one-byte fossil species `wFossilMon` `[ram/wram.asm:L2065]` before the revived fossil is received — in the English adaptation the resulting index is derived from a swapped party Pokémon's Attack stat modulo 256, so an Attack of 21 selects `$15`. What the repository establishes is only the ordinary revival data-flow: the lab stores the chosen fossil's species to `wFossilMon` `[engine/events/cinnabar_lab.asm:L52-53]`, and the later hand-over script passes that byte straight to `GivePokemon` `[scripts/CinnabarLabFossilRoom.asm:L77-80]`.
- Because the family is already published, this chapter documents it **only as excluded contrast** and never presents it as a novel method.

## Legal-input sequence

- Per the public corpus (external disclosure metadata), the setup is reachable with standard controller inputs on real hardware: it uses ordinary fossil revival, party/box menu ordering, and stat manipulation, all driven through menus and battles — no memory editor, cheat device, or save surgery. The repository citations support only the ordinary revival flow the technique corrupts: fossil selection into a register `[engine/events/cinnabar_lab.asm:L43-49]` and the hand-over grant `[scripts/CinnabarLabFossilRoom.asm:L77-80]`.
- This guide deliberately withholds any concrete recipe, stat value, or box/deposit ordering; only the *nature* of the mechanism is described.

## Code-cited mechanism

- When the player gives a fossil, the lab selects the corresponding species (`AERODACTYL`, `OMANYTE`, or `KABUTO`) into register `b` `[engine/events/cinnabar_lab.asm:L43-49]` and stores it to the one-byte `wFossilMon` `[engine/events/cinnabar_lab.asm:L52-53]` `[ram/wram.asm:L2065]`.
- Later, the fossil-room hand-over script reloads that byte and passes it directly to `GivePokemon` as the species (with level 30), performing no validation that the byte is a legitimate fossil species `[scripts/CinnabarLabFossilRoom.asm:L77-80]`:

```asm
	ld a, [wFossilMon]
	ld b, a
	ld c, 30
```

- The script then executes `call GivePokemon` on the next line `[scripts/CinnabarLabFossilRoom.asm:L77-80]`, and `GivePokemon` writes whatever species byte it receives in `b` straight into `wCurPartySpecies` before granting it `[home/give.asm:L18-21]`. If `wFossilMon` holds `$15`, the granted Pokémon is Mew `[constants/pokemon_constants.asm:L30]`.
- *External disclosure metadata:* the step that makes `wFossilMon` hold `$15` — the disclosed corruption of the stored fossil byte — is the disclosed technique; it is not a behavior of the revival routines cited above, which by themselves only ever store and return one of the three legitimate fossil species `[engine/events/cinnabar_lab.asm:L43-49]`.

## Catch step

- This family does not pass through the ball-throw flow: the Pokémon is *granted* by `GivePokemon` `[home/give.asm:L18-21]`, not captured through `ItemUseBall` `[engine/items/item_effects.asm:L104]` and its catch-rate comparison `[engine/items/item_effects.asm:L300-303]`.
- Any resulting Mew is produced by the disclosed corruption of `wFossilMon` `[ram/wram.asm:L2065]`, not by a legitimate capture. See the [Game mechanics reference](../02-game-mechanics-reference.md) for the normal capture pipeline this bypasses.

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.** The international fossil conversion glitch — and the original Japanese fossil conversion glitch it adapts — is in the public corpus, so it is excluded from the guide's novel-method search under R1 and is documented only as contrast.
- Capability note: unlike the bounded families, this technique **can** yield species `$15` `[constants/pokemon_constants.asm:L30]`, because `GivePokemon` grants whatever byte `wFossilMon` holds `[scripts/CinnabarLabFossilRoom.asm:L77-80]` `[home/give.asm:L18-21]`; it is excluded on novelty grounds (R1), not on capability.
- See [Novelty verification](../03-novelty-verification.md) for the disclosed-corpus comparison behind this verdict.

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes (controller-only, though not ordinary play).** The fossil-revival and stat/box-ordering steps are legitimate game inputs (external disclosure metadata); the repository citations support only the ordinary revival grant `[scripts/CinnabarLabFossilRoom.asm:L77-80]` `[home/give.asm:L18-21]` and the fossil selection it corrupts `[engine/events/cinnabar_lab.asm:L43-49]`.
- It nonetheless fails R1 as a disclosed technique, and this guide withholds any concrete sequence regardless of its input reachability.

## Limitations

- Excluded under R1 because the international fossil conversion glitch is already publicly disclosed.
- It **can** produce Mew, but only through the disclosed corruption of the stored fossil byte `wFossilMon` `[ram/wram.asm:L2065]` — not through a legitimate capture of species `$15` `[constants/pokemon_constants.asm:L30]`; the revival routines by themselves grant only the three legitimate fossil species `[engine/events/cinnabar_lab.asm:L43-49]`.
- Note on the `MEW`-token audit: because the grant consumes a runtime byte (`wFossilMon`) rather than a literal `MEW` symbol, the exact-`MEW` grep audit in [Conclusion and limitations](../04-conclusion-and-limitations.md) does not surface this path — a property of the audit's literal-token method, and precisely the unvalidated runtime grant the disclosed fossil family exploits `[scripts/CinnabarLabFossilRoom.asm:L77-80]`.
- Deliberately not reproduced as a recipe: no stat value, box ordering, or corruption sequence is given — only the ordinary revival routines the technique repurposes `[engine/events/cinnabar_lab.asm:L52-53]` `[home/give.asm:L18-21]`.
- For the full disclosed-corpus comparison and the reasoning behind this exclusion, see the [Novelty verification](../03-novelty-verification.md) chapter.
