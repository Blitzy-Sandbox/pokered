# MF-6: Link-trade state introduction

This chapter analyzes the Game Link trade path as a candidate route to obtaining Mew.

## Mechanism summary

- A link trade over the Game Link Cable exchanges a Pokémon between two connected cartridges through the Trade Center, a session driven by the cable-club routine `CableClub_DoBattleOrTrade` [engine/link/cable_club.asm:L4].
- The game records the active link session in `wLinkState`, a variable other routines read to branch on the current link state — for example, the shared link-battle PRNG reads it before generating a number [engine/battle/core.asm:L6543-6548].
- A trade only relays the bytes of a Pokémon that the sending cartridge already holds; the serial pipeline copies existing party data and never fabricates a new species [home/serial.asm:L1-18].
- Because Mew occupies species index `$15` [constants/pokemon_constants.asm:L30] but appears in no entry of the in-game trade table [data/events/trades.asm:L18-27], and a trade can relay only a Pokémon a cartridge already holds, no legitimate trade can introduce it [home/serial.asm:L1-18].

## Legal-input sequence

- The trade flow is entered entirely through standard inputs — the cable and the in-game menus — beginning from the cable-club session routine [engine/link/cable_club.asm:L4].

| Step | Input | Effect |
|------|-------|--------|
| 1 | Connect the Game Link Cable between two Game Boys | Establishes the serial link the `Serial` interrupt handler services [home/serial.asm:L1-18] |
| 2 | Enter the Cable Club and step onto the Trade Center link table | Starts the cable-club session through `CableClub_DoBattleOrTrade` [engine/link/cable_club.asm:L4] |
| 3 | Select the trade option | Sets `wLinkState` to `LINK_STATE_TRADING` for the session [engine/link/cable_club.asm:L274-278] |
| 4 | Choose a Pokémon to offer | Queues an existing party or box Pokémon for the multi-byte exchange [home/serial.asm:L55] |
| 5 | Confirm the trade | Exchanges the selected Pokémon's data over the serial port [home/serial.asm:L1-18] |

- Each step is driven by the normal cable-club session routine [engine/link/cable_club.asm:L4], so the sequence is fully R2-legal; however, the mechanism proof below shows that no reachable trade can carry species `$15` [data/events/trades.asm:L18-27].

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

- Bulk data such as the party structure is moved with the multi-byte routine `Serial_ExchangeBytes`, which copies the bytes of an already-owned Pokémon rather than synthesizing one [home/serial.asm:L55].
- The impossibility proof has two parts: first, the only NPC trades the game offers are the ten fixed `TradeMons` entries [data/events/trades.asm:L7]:

```asm
npctrade NIDORINO,   NIDORINA,  TRADE_DIALOGSET_CASUAL,    "TERRY"      ; used in ROUTE_11_GATE_2F
npctrade ABRA,       MR_MIME,   TRADE_DIALOGSET_CASUAL,    "MARCEL"     ; used in ROUTE_2_TRADE_HOUSE
```

- Those ten entries are NIDORINO, ABRA, BUTTERFREE, PONYTA, SPEAROW, SLOWBRO, POLIWHIRL, RAICHU, VENONAT, and NIDORAN_M — none of which is Mew [data/events/trades.asm:L18-27].
- Second, a player-to-player trade can likewise relay only a Pokémon that the partner cartridge already holds, so it too requires a pre-existing Mew to send [home/serial.asm:L1-18].
- Mew occupies species index `$15` [constants/pokemon_constants.asm:L30], and no obtainable source on either Red or Blue fills that index — the exhaustive cross-source audit is compiled in [Conclusion and limitations](../04-conclusion-and-limitations.md):

```asm
const MEW ; $15
```

- Therefore no legitimate trade — NPC or player-to-player — can introduce species `$15`, because neither channel has a Mew to send [data/events/trades.asm:L18-27].

## Catch step

- A traded Pokémon arrives already owned in the recipient's party or box, so it never passes through the wild-capture path that begins at `ItemUseBall` [engine/items/item_effects.asm:L104].
- That path's catch-rate comparison — the check that actually decides whether a Ball captures the target — is therefore never evaluated for a traded Pokémon [engine/items/item_effects.asm:L300-303]:

```asm
ld a, [wEnemyMonActualCatchRate]
cp b
jr c, .failedToCapture
```

- Since no Mew can be placed into a trade in the first place [data/events/trades.asm:L18-27], the catch step is moot; for the full capture pipeline see the [Game mechanics reference](../02-game-mechanics-reference.md).

## Novelty verdict (R1)

- Verdict: **IMPOSSIBLE (no source species)** — link trade is not a disclosed trick but an impossibility result, because a trade cannot generate a species that is absent from both connected cartridges [data/events/trades.asm:L18-27].
- It is therefore excluded from the novel-method set rather than presented as new; for the full adjudication see [Novelty verification](../03-novelty-verification.md).

## Inputs-only verdict (R2)

- Verdict: **Input-reachable = Yes** — the Game Link Cable used through the normal Trade Center flow is a legitimate game input, driven end-to-end by `CableClub_DoBattleOrTrade` [engine/link/cable_club.asm:L4].
- However, the outcome is **impossible for Mew**, because there is no source Mew anywhere in the trade data for the input sequence to relay [data/events/trades.asm:L18-27].

## Limitations

- Trade is a transfer, not a generator: the serial exchange only copies an existing Pokémon's bytes between cartridges [home/serial.asm:L55].
- The in-game trade table lists ten fixed species and none is Mew [data/events/trades.asm:L18-27].
- A player-to-player trade can only relay a Pokémon a cartridge already holds [home/serial.asm:L1-18], and Mew's species index `$15` [constants/pokemon_constants.asm:L30] is filled by no obtainable source on either cartridge, so the impossibility is symmetric — the exhaustive audit is in [Conclusion and limitations](../04-conclusion-and-limitations.md).
- Mew's catch rate of 45 [data/pokemon/base_stats/mew.asm:L7] is irrelevant here, since no Mew can enter a trade to reach a catch step at all [data/events/trades.asm:L18-27].
- For the shared capture and RNG primitives referenced above, see the [Game mechanics reference](../02-game-mechanics-reference.md).
