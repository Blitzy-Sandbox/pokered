# MF-6: Link-trade state introduction

This chapter analyzes the Game Link trade path as a candidate route to obtaining Mew.

## Mechanism summary

- A link trade over the Game Link Cable exchanges a Pokémon between two connected cartridges through the Trade Center, a session driven by the cable-club routine `CableClub_DoBattleOrTrade` [engine/link/cable_club.asm:L4].
- The game records the active link session in `wLinkState`, a variable other routines read to branch on the current link state — for example, the shared link-battle PRNG reads it before generating a number [engine/battle/core.asm:L6543-6548].
- A trade only relays the bytes of a Pokémon that the sending cartridge already holds. The trade logic copies from an already-owned party: in `TradeCenter_Trade` [engine/link/cable_club.asm:L682] the game removes the offered mon with `RemovePokemon` [engine/link/cable_club.asm:L800] and then copies the *received* cartridge's existing `wEnemyMons` entry into the party with `CopyData` followed by `AddEnemyMonToPlayerParty` [engine/link/cable_club.asm:L816-817], so no new species is ever fabricated.
- Because a trade relays only a Pokémon a cartridge already holds, it can transfer a pre-existing Mew but cannot originate or catch one; and since Mew `$15` [constants/pokemon_constants.asm:L30] appears in no in-game trade-table entry [data/events/trades.asm:L18-27] and — by a separate source audit — no obtainable source fills that index, two clean unmodified saves have no Mew for a trade to relay (the source-species-absence proof is independent of the relay mechanism and is compiled in [Conclusion and limitations](../04-conclusion-and-limitations.md)).

## Legal-input sequence

- The trade flow is entered entirely through standard inputs — the cable and the in-game menus — beginning from the cable-club session routine [engine/link/cable_club.asm:L4].

| Step | Input | Effect |
|------|-------|--------|
| 1 | Connect the Game Link Cable between two Game Boys | Establishes the serial link the `Serial` interrupt handler services [home/serial.asm:L1-18] |
| 2 | Enter the Cable Club and step onto the Trade Center link table | Starts the cable-club session through `CableClub_DoBattleOrTrade` [engine/link/cable_club.asm:L4] |
| 3 | Select the trade option | Sets `wLinkState` to `LINK_STATE_TRADING` for the session [engine/link/cable_club.asm:L274-278] |
| 4 | Choose a Pokémon to offer | Selects an existing party Pokémon whose data is placed in the exchanged party data block [engine/link/cable_club.asm:L131-135] |
| 5 | Confirm the trade | Swaps the party data block between the two Game Boys, then installs the received mon via the `TradeCenter_Trade` remove/copy/add sequence [engine/link/cable_club.asm:L131-135], [engine/link/cable_club.asm:L800] |

- Each step is driven by the normal cable-club session routine [engine/link/cable_club.asm:L4], so the sequence is fully R2-legal; however, the mechanism analysis below shows a trade is transfer-only — it can relay a pre-existing Mew but cannot originate or catch one, and a clean-save trade has no Mew to carry [data/events/trades.asm:L18-27].

## Code-cited mechanism

- When the player selects a session on the link table, `CableClub_DoBattleOrTrade` writes the session type into `wLinkState` — `LINK_STATE_TRADING` for a trade, `LINK_STATE_BATTLING` for a battle [engine/link/cable_club.asm:L274-278]:

```asm
ld a, LINK_STATE_TRADING
ld [wLinkState], a
```

- The link-session state values are defined in the serial constants, for example `LINK_STATE_BATTLING` [constants/serial_constants.asm:L25].
- Once the session is trading, the two cartridges exchange bytes through the serial registers `rSB`/`rSC` inside the `Serial` interrupt handler [home/serial.asm:L1-18]:

```asm
ldh a, [rSB]
ldh [hSerialReceiveData], a
```

- The party structure itself is exchanged by the trade routine: `CableClub_DoBattleOrTrade` points `hl`/`de` at the player and enemy party data blocks and calls `Serial_ExchangeBytes` to swap them [engine/link/cable_club.asm:L131-135], the low-level transport being the multi-byte routine `Serial_ExchangeBytes` [home/serial.asm:L55]. The exchange moves the bytes of already-owned party mons; it never synthesizes a new species.
- The transfer-only bound has two parts. First, the only NPC trades the game offers are the ten fixed `TradeMons` entries [data/events/trades.asm:L18-27]:

```asm
npctrade NIDORINO,   NIDORINA,  TRADE_DIALOGSET_CASUAL,    "TERRY"      ; used in ROUTE_11_GATE_2F
npctrade ABRA,       MR_MIME,   TRADE_DIALOGSET_CASUAL,    "MARCEL"     ; used in ROUTE_2_TRADE_HOUSE
```

- Those ten entries are NIDORINO, ABRA, BUTTERFREE, PONYTA, SPEAROW, SLOWBRO, POLIWHIRL, RAICHU, VENONAT, and NIDORAN_M — none of which is Mew [data/events/trades.asm:L18-27].
- Second, a player-to-player trade can likewise relay only a Pokémon that the partner cartridge already holds: the received mon is copied from the partner's existing `wEnemyMons` data by `CopyData` and `AddEnemyMonToPlayerParty` [engine/link/cable_club.asm:L816-817], so it too requires a pre-existing Mew to send.
- Mew occupies species index `$15` [constants/pokemon_constants.asm:L30], and no obtainable source on either Red or Blue fills that index — the exhaustive cross-source audit is compiled in [Conclusion and limitations](../04-conclusion-and-limitations.md):

```asm
const MEW ; $15
```

- Therefore a trade between two clean, unmodified saves has no Mew to send through either channel; a trade can still relay a Mew that already exists on a cartridge, but that only transfers a pre-existing Mew rather than originating or catching one [data/events/trades.asm:L18-27].

## Catch step

- A traded Pokémon arrives already owned in the recipient's party or box, so it never passes through the wild-capture path that begins at `ItemUseBall` [engine/items/item_effects.asm:L104].
- That path's catch-rate comparison — the check that actually decides whether a Ball captures the target — is therefore never evaluated for a traded Pokémon [engine/items/item_effects.asm:L300-303]:

```asm
ld a, [wEnemyMonActualCatchRate]
cp b
jr c, .failedToCapture
```

- A received Mew is not caught — it arrives already-owned — so the catch step never applies to a trade, and a clean-save trade has no Mew to relay in the first place [data/events/trades.asm:L18-27]; for the full capture pipeline see the [Game mechanics reference](../02-game-mechanics-reference.md).

## Novelty verdict (R1)

- Verdict: **EXCLUDED — transfer-only (cannot originate or catch Mew).** A link trade is not a disclosed trick; it is a transfer channel that can relay a pre-existing Mew but cannot generate or catch one, and between two clean unmodified saves there is no Mew to relay [data/events/trades.asm:L18-27].
- It is therefore excluded from the novel-method set rather than presented as new; any Mew a trade could relay owes its provenance to another family (a disclosed glitch or an external/event source), not to the trade itself. For the full adjudication see [Novelty verification](../03-novelty-verification.md).

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes** — the Game Link Cable used through the normal Trade Center flow is a legitimate game input, driven end-to-end by `CableClub_DoBattleOrTrade` [engine/link/cable_club.asm:L4].
- However, it is **transfer-only for Mew**: the input sequence can relay a pre-existing Mew but cannot originate or catch one, and a clean-save trade has no source Mew to relay [data/events/trades.asm:L18-27]. Receiving an externally-sourced or event Mew by trade is not a qualifying catch or origination under R2.

## Limitations

- Trade is a transfer, not a generator: the trade routine copies an existing mon's bytes from the partner's party data into the recipient's party [engine/link/cable_club.asm:L816-817], carried over the low-level serial transport [home/serial.asm:L55].
- The in-game trade table lists ten fixed species and none is Mew [data/events/trades.asm:L18-27].
- A player-to-player trade can only relay a Pokémon a cartridge already holds — the received mon is copied from the partner's existing party data [engine/link/cable_club.asm:L816-817]. Separately, Mew's species index `$15` [constants/pokemon_constants.asm:L30] is filled by no obtainable source on either cartridge (the exhaustive `MEW`-symbol audit is in [Conclusion and limitations](../04-conclusion-and-limitations.md)), so two clean saves have no Mew to exchange; a trade can still relay a pre-existing Mew, but that transfers rather than originates or catches it.
- Mew's catch rate of 45 [data/pokemon/base_stats/mew.asm:L7] is irrelevant here: a traded Pokémon is received already-owned and never reaches a catch step, and a clean-save trade has no Mew to trade in any case [data/events/trades.asm:L18-27].
- For the shared capture and RNG primitives referenced above, see the [Game mechanics reference](../02-game-mechanics-reference.md).
