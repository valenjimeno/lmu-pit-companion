# LMU Pit Companion User Guide

**Documented version:** 0.25.2  
**Game:** Le Mans Ultimate  
**Platform:** SimHub on Windows

## Overview

LMU Pit Companion estimates how much fuel or Virtual Energy you need to finish and can apply that target directly to the LMU Pit Menu.

The estimate is based on recent lap consumption. Complete a few representative laps before relying on it, and always check the in-game Pit Menu before entering the pits.

## Installation

1. Close SimHub.
2. Extract the downloaded release ZIP.
3. Copy `LMUPitCompanionPlugin.dll` to the SimHub installation folder, normally `C:\Program Files (x86)\SimHub\`.
4. Start SimHub.
5. Open **LMU Pit Companion** from the sidebar.
6. Assign the actions you want and select **Save and apply**.

To update, close SimHub and replace the existing DLL. Your settings remain in SimHub.

## Strategy calculation

The plugin automatically chooses the appropriate mode:

- **VE** for cars using Virtual Energy.
- **FUEL** for cars using conventional fuel targets.

It measures recent consumption, estimates the remaining laps, and adds the configured safety margin and reserve. Pit laps, refuelling, and implausible samples are excluded where appropriate.

Two targets are available:

- **General target:** a conservative target from the current position.
- **This-pit target:** intended for a stop at the end of the current lap.

`ActiveReliable = 1` and `StrategyStatus = READY` indicate that enough valid samples have been collected.

## Settings

| Setting | Purpose | Default |
| --- | --- | ---: |
| Safety laps | Adds consumption for a fraction of a lap. | 0.5 |
| Virtual Energy reserve | Adds a fixed VE percentage reserve. | 2% |
| Fuel reserve | Adds a fixed fuel reserve. | 1 L |
| Automatically avoid refuelling | Selects the minimum refill when the car can already finish. | Off |
| Automatically apply this-pit strategy | Applies the recommended target when a new pit request is detected. | Off |
| Diagnostics log | Records information useful for troubleshooting. | On |

Selecting **Save and apply** resets the collected samples, so the strategy must become reliable again.

## Actions

| SimHub action | What it does |
| --- | --- |
| `LMUPitCompanionPlugin.ApplyStrategy` | Applies the general finish target. |
| `LMUPitCompanionPlugin.ApplyStrategyThisPit` | Applies the target for stopping this lap. |
| `LMUPitCompanionPlugin.NoRefuel` | Selects the minimum fuel or VE value. |
| `LMUPitCompanionPlugin.TiresNoChange` | Cancels all selected tyre changes. |
| `LMUPitCompanionPlugin.TiresAllCurrent` | Changes all tyres using their fitted compounds. |
| `LMUPitCompanionPlugin.TiresFrontCurrent` | Changes only the front tyres. |
| `LMUPitCompanionPlugin.TiresRearCurrent` | Changes only the rear tyres. |
| `LMUPitCompanionPlugin.TiresAllWet` | Changes all four tyres to wets. |

Actions can be assigned to a keyboard, steering wheel, Stream Deck, button box, or another controller supported by SimHub.

## Pit-request automation

When automation is enabled and a new pit request is detected, the plugin follows this priority:

1. Keep an existing manual fuel or VE selection.
2. Select no refuelling if the current level is sufficient and that option is enabled.
3. Otherwise apply the this-pit strategy if automatic application is enabled.

The plugin rechecks the session, lap, pit request, telemetry, and manual selection before changing the menu.

## Useful dashboard properties

All properties use the `LMUPitCompanionPlugin.` prefix.

| Property | Meaning |
| --- | --- |
| `StrategyStatus` | Current calculation or action status. |
| `ActiveMode` | `VE` or `FUEL`. |
| `ActiveUnit` | `%` or `L`. |
| `ActiveReliable` | `1` when enough valid samples exist. |
| `ActiveCurrent` | Current fuel or VE. |
| `ActiveTarget` | General finish target. |
| `ThisPitTarget` | Target for stopping this lap. |
| `ThisPitAmountToAdd` | Estimated amount added at the stop. |
| `LapsToEnd` | Estimated remaining laps. |
| `LastResult` | Result of the latest strategy action. |
| `LastTireResult` | Result of the latest tyre action. |
| `PitStopRequested` | `1` requested, `0` clear, `-1` unavailable. |
| `AutoNoRefuelStatus` | Latest automation decision or result. |

Mode-specific properties are also available: `HasVE`, `VEPerLap`, `VEToEnd`, `VEReliable`, `FuelPerLap`, `FuelToEnd`, and `FuelReliable`.

## Common statuses

| Status | Meaning |
| --- | --- |
| `WARMUP` | More representative laps are needed. |
| `READY` | The strategy is available and reliable. |
| `NOT_RELIABLE` | The selected action needs more valid samples. |
| `TARGET_UNAVAILABLE` | Current telemetry cannot produce a target. |
| `TARGET_OUT_OF_RANGE` | The required target is unavailable in the Pit Menu. |
| `WORKING` | A Pit Menu operation is in progress. |
| `APPLIED` | The change was applied and verified. |
| `BUSY` | Another operation is already running. |
| `ERROR_*` | The operation failed or could not be verified. |

## Troubleshooting

### Strategy remains in `WARMUP`

Complete more representative laps without entering the pits. Saving settings also resets the collected samples.

### `ERROR_API_TIMEOUT` or `ERROR_API_UNAVAILABLE`

Make sure LMU is running in an active session and its Pit Menu is available, then try again.

### Verification or rollback error

Check every selection in the LMU Pit Menu manually before entering the pits.

### Diagnostics

When enabled, the log is normally stored at:

```text
C:\Program Files (x86)\SimHub\Logs\LMUPitCompanion.log
```

Review the log before attaching it to a public issue because it may contain session and telemetry details.

## Important limitation

The strategy is an estimate based on recent consumption. Weather, traffic, safety-car periods, damage, driving style, and pace changes can affect the real requirement. The driver remains responsible for the final pit-stop settings.
