# LMU Pit Companion v0.33.0

Switch from wets to dry tyres directly, or use one button to cycle through the compounds available for your car.

## New tyre actions

| SimHub action | Selection and fallback order |
| --- | --- |
| `LMUPitCompanionPlugin.TiresAllSoft` | Soft → Medium → Hard |
| `LMUPitCompanionPlugin.TiresAllMedium` | Medium → Hard → Soft |
| `LMUPitCompanionPlugin.TiresAllHard` | Hard → Medium → Soft |
| `LMUPitCompanionPlugin.TiresCycleCompound` | Soft → Medium → Hard → Wet → Soft, skipping unavailable compounds |

The direct dry actions work even when wets are fitted. They prefer a new set of the selected compound, then a used set of that compound before trying the fallback compound. Only Soft, Medium and Hard are recognised as dry compounds; if none is available, the action fails without writing.

The cycle starts from the current global Pit Menu selection on every press, so it follows manual menu changes too. From No Change, mixed or unrecognised selections, it starts at the first available compound in the cycle. A compound must be selectable globally and at all four corners. New sets are preferred, then used sets. With one available compound, the cycle keeps that compound; with none, it fails without writing.

All four actions have English and Spanish controls. Writes preserve complete menu objects, verify all four tyres and unrelated settings, and attempt to restore the original menu if verification fails. Existing action bindings, dashboard properties, settings and consumption history remain compatible.

## Included fixes from 0.32.1 and 0.32.2

- Reliable matching consumption history is available immediately at race start and remains active until the live rolling window reaches its reliability threshold.
- Initial timed-race targets can use LMU's estimated lap time or SimHub's all-time best before a valid session best exists.
- A stable starting fuel or Virtual Energy selection that differs from LMU's protocol default no longer blocks automatic no-refuelling or this-pit application. Selection changes between the two safety reads still cancel the automatic write.

## Installation

1. Close SimHub.
2. Extract `LMUPitCompanion-0.33.0.zip`.
3. Replace `LMUPitCompanionPlugin.dll` in the SimHub installation directory, normally `C:\Program Files (x86)\SimHub\`.
4. Start SimHub and assign the new actions in the tyre section. Existing bindings are retained.

Settings and consumption history are retained. Always check the in-game Pit Menu before entering the pits.

## Verification

- Plugin version: `0.33.0.0`.
- Automated suite: 2,244 assertions passed, including all 15 nonempty supported compound catalogues, complete cycles, dry fallbacks, verification, cancellation and rollback scenarios.
- Release build and English/Spanish settings UI smoke test passed against the installed SimHub DLLs.
- The packaged DLL is accompanied only by README, changelog and user guide. No source, projects, scripts, tests, symbols, local settings, logs, backups or consumption history are included.
- Live LMU tyre writes have not been exercised as part of this release preparation; new protocol behaviour remains to be confirmed in a controlled game session.

The SHA-256 is supplied in `LMUPitCompanion-0.33.0.zip.sha256`.
