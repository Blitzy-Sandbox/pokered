# MF-1: Special-stat / interrupted-battle encounter

## Mechanism summary

- Publicly this family is known as the **Mew glitch**, the **long-range Trainer glitch**, the **Trainer-Fly glitch**, and the **Ditto (Special-stat) trick**; those names are disclosure labels only and are not cited as evidence of game behavior.
- The family interrupts the normal battle-start flow so that a value left in memory is copied into the enemy-species field by `InitOpponent` `[engine/battle/core.asm:L6647-6650]` and is then reinterpreted as a wild-species index when the battle-setup path subtracts the trainer-id offset from that byte and branches to a wild battle `[engine/battle/core.asm:L6674-6676]`.
- Mew occupies internal species index `$15`, i.e. 21 decimal, so a leftover Special value of 21 is exactly the byte that selects it `[constants/pokemon_constants.asm:L30]`.
- This technique is **DISCLOSED** and appears here **only as an excluded contrast**; it is never presented as this guide's novel method.

## Legal-input sequence

- The disclosed glitch is reachable with standard controller inputs and precise timing; no memory editing, cheat device, or peripheral outside normal play is involved. The table below is deliberately high-level — enough to establish input-reachability (R2), not a reproducible recipe.

| Step | Input | Effect |
| --- | --- | --- |
| 1 | Trigger a distant (long-range) Trainer's sighting, then open the Start menu on the trigger frame to defer the battle | The engaged-trainer state is written by `EngageMapTrainer` before the battle is fully set up, leaving the engagement pending `[home/trainers.asm:L327-338]` |
| 2 | While the battle is deferred, arrange for the shared engaged-trainer byte to hold 21 (in the disclosed recipe, by viewing or using a Pokémon whose Special stat is 21) | The enemy-stat buffers and `wEngagedTrainerClass` occupy the same `UNION` storage, so a stat-derived value of 21 lands in the byte later read as the opponent id `[ram/wram.asm:L525-582]` |
| 3 | Close the menu and let the deferred battle resolve | `InitBattleEnemyParameters` copies `wEngagedTrainerClass` into `wCurOpponent` `[home/trainers.asm:L233-235]`, `InitOpponent` copies that into the enemy-species field `[engine/battle/core.asm:L6647-6650]`, and the wild-vs-trainer branch reads 21 as wild species `$15` = Mew `[engine/battle/core.asm:L6674-6676]` |
| 4 | Throw a Poké Ball | Hands off to the ordinary catch path (see Catch step) |

- The full, precise public steps for this family are disclosed elsewhere (catalogued in the novelty chapter's corpus) and are out of scope to reproduce here, because R1 explicitly excludes them.

## Code-cited mechanism

The family is an emergent misuse of the ordinary battle-setup path rather than a separate routine, yet the exact code that lets a retained stat value become a wild species can be cited step by step.

### The engaged-trainer alias chain

- The retail WRAM defines a 39-byte `UNION` that overlays two layouts on the same storage: in one layout the bytes hold the player and enemy unmodified-stat buffers (ending in `wEnemyMonUnmodifiedSpecial`), and in the other they hold `wEngagedTrainerClass` and `wEngagedTrainerSet` `[ram/wram.asm:L525-582]`. `wEngagedTrainerClass` therefore shares its storage with the enemy Special-stat buffer word `wEnemyMonUnmodifiedSpecial` `[ram/wram.asm:L563]`, `[ram/wram.asm:L580]`.
- During normal play the enemy-stat buffers are populated from the current enemy Pokémon: when enemy data is loaded, `CopyData` copies its level and stats into `wEnemyMonUnmodified{Level..Special}` `[engine/battle/core.asm:L6138-6141]`. A value derived from a Pokémon's Special stat can thus persist in the union storage that `wEngagedTrainerClass` reads.
- The engaged-trainer field itself is written from map data when a trainer engages, in `EngageMapTrainer` `[home/trainers.asm:L327-338]`:

```asm
	ld a, [hli]    ; load trainer class
	ld [wEngagedTrainerClass], a
```

- When the deferred battle is set up, `InitBattleEnemyParameters` copies `wEngagedTrainerClass` straight into `wCurOpponent` `[home/trainers.asm:L233-235]`:

```asm
	ld a, [wEngagedTrainerClass]
	ld [wCurOpponent], a
```

### From `wCurOpponent` to a wild species

- Continuing the chain, when the battle is set up `InitOpponent` copies the opponent id from `wCurOpponent` into both the current-party-species and enemy-species fields `[engine/battle/core.asm:L6647-6650]`:

```asm
	ld a, [wCurOpponent]
	ld [wCurPartySpecies], a
	ld [wEnemyMonSpecies2], a
```

- The battle-setup path then decides whether the battle is wild or trainer by subtracting the trainer-id offset from that enemy-species byte; when the byte is below the offset the subtraction sets the carry flag and the game branches to a wild battle, keeping the byte as the wild-species index `[engine/battle/core.asm:L6674-6676]`:

```asm
	ld a, [wEnemyMonSpecies2]
	sub OPP_ID_OFFSET
	jp c, InitWildBattle
```

- `OPP_ID_OFFSET` is defined as 200, so any retained value below 200 is treated as a wild-species index rather than a trainer class `[constants/trainer_constants.asm:L1]`. A leftover value of 21 is therefore read as wild species index `$15`, which is Mew `[constants/pokemon_constants.asm:L30]`.
- The enemy Pokémon's data occupies the `wEnemyMon` battle buffer `[ram/wram.asm:L1198]`, and the species byte the branch above tests is held in the adjacent `wEnemyMonSpecies2` field `[ram/wram.asm:L1193]`.
- In ordinary play this same enemy-species field is instead filled by the wild-encounter generator, which reads a byte from the current map's table; that generator is entered from the overworld through a single far-call `[engine/battle/core.asm:L6664]`:

```asm
	callfar TryDoWildEncounter
```

- The disclosed technique's only contribution is a matter of timing: it defers a trainer engagement so that the deferred battle is set up through the engaged-trainer path (`InitBattleEnemyParameters` → `wCurOpponent` → `InitOpponent`) while the shared union byte holds 21, rather than through the ordinary wild-encounter far-call. The reinterpretation itself is performed entirely by the stock copies and the wild-vs-trainer branch cited above `[home/trainers.asm:L233-235]`, `[engine/battle/core.asm:L6647-6650]`, `[engine/battle/core.asm:L6674-6676]`; the published recipe arranges the value 21, and this guide does not reproduce that recipe because R1 excludes it.
- Once the resulting wild battle has begun, its random values come from `BattleRandom`, which uses a shared PRNG list only during link battles and otherwise falls straight through to `Random` `[engine/battle/core.asm:L6543-6548]`:

```asm
	ld a, [wLinkState]
	cp LINK_STATE_BATTLING
	jp nz, Random
```

## Catch step

- Once the glitched Mew battle has begun, it is captured through the ordinary item path: `ItemUseBall` runs the standard Poké Ball routine `[engine/items/item_effects.asm:L104]`, and capture succeeds or fails via the catch-rate comparison `[engine/items/item_effects.asm:L300-303]` evaluated against Mew's catch rate of 45 `[data/pokemon/base_stats/mew.asm:L7]`:

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
