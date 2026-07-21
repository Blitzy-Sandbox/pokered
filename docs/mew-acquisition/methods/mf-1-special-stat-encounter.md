# MF-1: Special-stat / interrupted-battle encounter

## Mechanism summary

- Publicly this family is known as the **Mew glitch**, the **long-range Trainer glitch**, the **Trainer-Fly glitch**, and the **Ditto (Special-stat) trick**; those names are disclosure labels only and are not cited as evidence of game behavior.
- The family interrupts the normal battle-start flow so that a value left in the enemy Pokémon's Special stat is later reinterpreted as the wild-species index, reusing the same enemy-species fields the encounter generator normally fills [engine/battle/wild_encounters.asm:L79-80].
- Mew occupies internal species index `$15`, i.e. 21 decimal, so a leftover Special value of 21 is exactly the byte that selects it [constants/pokemon_constants.asm:L30].
- This technique is **DISCLOSED** and appears here **only as an excluded contrast**; it is never presented as this guide's novel method.

## Legal-input sequence

- The disclosed glitch is reachable with standard controller inputs and precise timing; no memory editing, cheat device, or peripheral outside normal play is involved. The table below is deliberately high-level — enough to establish input-reachability (R2), not a reproducible recipe.

| Step | Input | Effect |
| --- | --- | --- |
| 1 | Arrange for the last Pokémon seen or fought to have a Special of 21 (for example, by battling a species whose Special is 21) | Leaves the value 21 in the enemy-stat memory the glitch later reinterprets |
| 2 | Walk into a distant Trainer's line of sight, then open the Start menu on the trigger frame | Interrupts the normal battle-start sequence before a wild species is read from a map table |
| 3 | Close the menu and let the interrupted special encounter resolve | The stored Special value is consumed as the species index, producing a Mew battle |
| 4 | Throw a Poké Ball | Hands off to the ordinary catch path (see Catch step) |

- The full, precise public steps for this family are disclosed elsewhere (catalogued in the novelty chapter's corpus) and are out of scope to reproduce here, because R1 explicitly excludes them.

## Code-cited mechanism

- There is no dedicated "glitch routine" in the source; the family is an emergent misuse of the ordinary encounter and battle-setup paths, so the mechanism is grounded in those normal paths below.
- In a normal wild encounter the species byte is read from the current map's table and written to both the current-party-species and enemy-species fields [engine/battle/wild_encounters.asm:L66-80]:

```asm
	ld a, [hl]
	ld [wCurPartySpecies], a
	ld [wEnemyMonSpecies2], a
```

- Those two stores are the exact fields the glitch ultimately controls [engine/battle/wild_encounters.asm:L79-80], and the wild-encounter routine that performs them is entered from the overworld through a single far-call [engine/battle/core.asm:L6664]:

```asm
	callfar TryDoWildEncounter
```

- The enemy Pokémon's data occupies the `wEnemyMon` battle buffer [ram/wram.asm:L1198], and the species byte that decides which Pokémon appears is held in the adjacent `wEnemyMonSpecies2` field [ram/wram.asm:L1193].
- Wild battles draw their random values from `BattleRandom`, which uses a shared PRNG list only during link battles and otherwise falls straight through to `Random` [engine/battle/core.asm:L6543-6548]:

```asm
	ld a, [wLinkState]
	cp LINK_STATE_BATTLING
	jp nz, Random
```

- The disclosed glitch subverts this flow by interrupting the sequence before `TryDoWildEncounter` performs its table read [engine/battle/core.asm:L6664]; the enemy-species field is then populated from a leftover Special-stat value of 21 rather than a table byte, and because index `$15` is Mew that value resolves the encounter to a Mew battle [constants/pokemon_constants.asm:L30].
- This is described at the adjudication level only; the source contains no distinct "Mew-glitch" subroutine to cite, which is why the mechanics above are grounded in the normal paths the glitch abuses.

## Catch step

- Once the glitched Mew battle has begun, it is captured through the ordinary item path: `ItemUseBall` runs the standard Poké Ball routine [engine/items/item_effects.asm:L104], and capture succeeds or fails via the catch-rate comparison [engine/items/item_effects.asm:L300-303] evaluated against Mew's catch rate of 45 [data/pokemon/base_stats/mew.asm:L7]:

```asm
	ld a, [wEnemyMonActualCatchRate]
	cp b
	jr c, .failedToCapture
```

- The full capture pipeline — guards, shake count, and catch-rate arithmetic — is described in the [Game mechanics reference](../02-game-mechanics-reference.md).

## Novelty verdict (R1)

- Verdict: **DISCLOSED → EXCLUDED.**
- This family **is** the R1 named-excluded **Mew glitch** and **Trainer-Fly glitch**, together with the **long-range Trainer glitch** and the **Ditto (Special-stat) trick**; it is already published and therefore cannot be this guide's novel method.
- See the [Novelty verification](../03-novelty-verification.md) chapter for the full disclosed-corpus comparison and the per-candidate verdict matrix.

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes.**
- The technique is achievable with standard controller inputs and frame-precise timing alone, requiring no memory editing, cheat cartridge, or external peripheral — so it satisfies R2, yet it still fails R1 because it is disclosed.

## Limitations

- Excluded by R1: this is one of the techniques the user's restriction names explicitly (the Mew glitch and the Trainer-Fly glitch), so it is disqualified as a novel method.
- Documented here only as an excluded contrast, to define the disclosed baseline against which the other candidate mechanism families are adjudicated.
- Not the guide's novel method and never presented as one; it is a member of the already-published corpus.
- For the complete disclosed-corpus comparison and the deciding sources, the reader is directed to the [Novelty verification](../03-novelty-verification.md) chapter.
