# LMU Pit Companion User Guide

**Documented version:** 0.31.0<br>
**Game:** Le Mans Ultimate<br>
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

## Pre-race starting load

The plugin stores a compact consumption history after valid laps. Profiles are separated by the physical LMU vehicle package and exact circuit layout, not by team name. Only aggregate statistics and recent weighted values are retained; individual laps are not stored indefinitely.

Before a race, the plugin combines:

- the timed duration or configured lap count reported through SimHub;
- a native or historical reference lap time;
- historical fuel or Virtual Energy consumption for the selected car and layout;
- the configured safety-lap margin and timed-boundary protection.

When `PreRaceEstimateStatus` is `READY`, run `LMUPitCompanionPlugin.ApplyPreRaceLoad`. The action detects whether LMU exposes `VM_FUEL_LEVEL` or `VM_VIRTUAL_ENERGY`, writes the corresponding garage value, and reads it back for verification. The manual action remains available independently of automatic loading.

To use automatic starting load, enable **Automatically apply starting fuel or VE** in **Automation**, then select **Save and apply**. It is disabled by default. The plugin makes one automatic attempt after a valid garage recommendation has remained stable for three seconds. It uses the configured historical consumption and margins, without requiring the Car Setup tab to be opened.

Before joining the track, wait for `PreRaceApplyStatus` to report `APPLIED_VERIFIED` or `ALREADY_SET`. Leaving immediately can prevent the recommendation from being applied. LMU may keep displaying the old value in its menu; check the actual starting load on the in-car HUD.

Driving locks automatic loading for that race. Returning to the garage, losing telemetry, changing the SimHub session GUID, or disabling and re-enabling the option does not clear this lock. Another observed session type, such as practice or qualifying, or a garage-confirmed change of physical car model or circuit layout re-arms it. A new race with the same car and layout may remain locked if no such transition was observed; use the manual pre-race action in that case. This conservative behavior will be improved as further session transitions are validated.

`AutoPreRaceEnabled` exposes the saved option, and `AutoPreRaceStatus` shows the current state: `DISABLED`, `WAITING_FOR_GARAGE`, `WAITING_FOR_ESTIMATE`, `WAITING_FOR_GARAGE_SETTLE`, `ATTEMPTED`, or `RACE_LOCKED`. The outcome of the write appears separately in `PreRaceApplyStatus`. Disabling automatic loading does not restore an earlier setup value.

The first completed lap establishes a baseline and is not itself a consumption sample. The pre-race recommendation requires the configured minimum number of later accepted samples, two by default. If there is insufficient history or the car, layout, session, or garage mode cannot be identified reliably, the action stops without changing the setup.

The local history file is stored at:

```text
%LOCALAPPDATA%\SimHub\LMUPitCompanion\consumption-history.json
```

## Settings

History identity is resolved from the garage in practice and qualifying as well as before a race. These identity-only reads do not apply starting fuel. History is recorded and consulted only when both a trusted physical model and a garage-confirmed circuit variant are available; otherwise `HistoryStatus` reports `NO_IDENTITY`. Live consumption remains available.

| Setting | Purpose | Default |
| --- | --- | ---: |
| Safety laps | Adds consumption for a fraction of a lap. | 0.5 |
| Virtual Energy reserve | Adds a fixed VE percentage reserve. | 2% |
| Fuel reserve | Adds a fixed fuel reserve. | 1 L |
| Generate consumption history | Records accepted completed laps in the local car-and-layout profile. Turning it off keeps existing profiles but stops new samples. | On |
| Automatically apply starting fuel or VE | Makes one verified starting-load attempt in a stable race garage. Available only while history generation is enabled. | Off |
| Automatically avoid refuelling | Selects the minimum refill when the car can already finish. | Off |
| Automatically apply this-pit strategy | Applies the recommended target when a new pit request is detected. | Off |
| Diagnostics log | Records information useful for troubleshooting. | On |

Selecting **Save and apply** resets the collected samples, so the strategy must become reliable again.

Disabling **Generate consumption history** immediately clears and disables **Automatically apply starting fuel or VE**. Re-enabling history makes the automatic option selectable again but does not turn it back on. This prevents an automatic garage write from depending on a data source that the driver has explicitly disabled.

## Actions

| SimHub action | What it does |
| --- | --- |
| `LMUPitCompanionPlugin.ApplyPreRaceLoad` | Applies the recommended starting fuel or Virtual Energy in the race garage. |
| `LMUPitCompanionPlugin.ApplyStrategy` | Applies the general finish target. |
| `LMUPitCompanionPlugin.ApplyStrategyThisPit` | Applies the target for stopping this lap. |
| `LMUPitCompanionPlugin.NoRefuel` | Selects the minimum fuel or VE value. |
| `LMUPitCompanionPlugin.TiresNoChange` | Cancels all selected tyre changes. |
| `LMUPitCompanionPlugin.TiresAllCurrent` | Changes all tyres using their fitted compounds. |
| `LMUPitCompanionPlugin.TiresFrontCurrent` | Changes only the front tyres. |
| `LMUPitCompanionPlugin.TiresRearCurrent` | Changes only the rear tyres. |
| `LMUPitCompanionPlugin.TiresLeftCurrent` | Changes only the left-side tyres (`FL + RL`) using their fitted compounds. |
| `LMUPitCompanionPlugin.TiresRightCurrent` | Changes only the right-side tyres (`FR + RR`) using their fitted compounds. |
| `LMUPitCompanionPlugin.TiresFrontLeftCurrent` | Changes only the front-left tyre (`FL`) using its fitted compound. |
| `LMUPitCompanionPlugin.TiresFrontRightCurrent` | Changes only the front-right tyre (`FR`) using its fitted compound. |
| `LMUPitCompanionPlugin.TiresRearLeftCurrent` | Changes only the rear-left tyre (`RL`) using its fitted compound. |
| `LMUPitCompanionPlugin.TiresRearRightCurrent` | Changes only the rear-right tyre (`RR`) using its fitted compound. |
| `LMUPitCompanionPlugin.TiresAllWet` | Changes all four tyres to wets. |

Axle, side, and individual-wheel actions first cancel any previous tyre selection. They then select only the requested tyres, preserving the compound fitted to each selected wheel. This prevents an earlier action from leaving additional tyres enabled.

For conventional fuel, strategy actions convert the displayed total finish target into the amount that must be added to the fuel already in the car. If the complete fuel or VE requirement does not fit in one stint, the action selects the largest value exposed by LMU for the next stint while preserving the full-race target in the dashboard properties. For current-compound tyre actions, a new set is preferred; when none is available, the plugin selects the first used set of the same fitted compound. It never deliberately substitutes a different compound.

The menu-target properties expose the value that the action will actually request:

- In **FUEL** mode, `GeneralMenuTarget` and `ThisPitMenuTarget` are litres to add, rounded up. For example, if the car has 23.4 L and the finish target is 70 L, the menu target is 47 L.
- In **VE** mode, the menu target is the absolute percentage selected in LMU. A calculated full-race demand of 1079% remains visible as the strategy target, while the menu target is capped to the highest available value, normally 100%.
- If the exact conventional-fuel value is not exposed by LMU, the plugin chooses the smallest available value that is sufficient. If the full request cannot fit, it chooses the maximum available value for the next stint.

Use `ActiveUnit` to label these values on a dashboard: `L` for conventional fuel and `%` for Virtual Energy.

Actions can be assigned to a keyboard, steering wheel, Stream Deck, button box, or another controller supported by SimHub.

## Pit-request automation

When automation is enabled and a new pit request is detected, the plugin follows this priority:

1. Keep an existing manual fuel or VE selection.
2. Select no refuelling if the current level is sufficient and that option is enabled.
3. Otherwise apply the this-pit strategy if automatic application is enabled.

The plugin rechecks the session, lap, pit request, telemetry, and manual selection before changing the menu.

## Consumption history

The **Generate consumption history** option is enabled by default. The history is local to the PC and uses one profile for each trusted physical LMU vehicle package and exact circuit layout. A different team or livery using the same physical package shares the profile; a different layout does not.

The first completed lap establishes the consumption baseline. Later completed laps can become samples when the plugin has a valid lap transition, positive and plausible consumption, and a trusted car-and-layout identity. Pit laps, refuelling or an increase in stored energy, missing values, non-positive consumption, implausible consumption, and unresolved identity are rejected. A rejected lap is not added to the aggregate.

The file stores compact aggregate statistics, recent-weighted means, sample counts, variability, and the latest update time. It does not retain an unlimited list of individual laps. The recent-weighted value lets newer representative driving influence the estimate without discarding the longer-term profile. The catalog is bounded to 256 profiles; if that limit is reached, the least recently used profile is removed when a new one is created.

Identity can be confirmed from the garage in practice, qualifying, or race sessions. These identity-only reads never apply a starting load. Until both the physical car and circuit variant are trustworthy, `HistoryStatus` is `NO_IDENTITY`; live-session consumption can still work independently.

Disable history generation to stop adding new accepted laps. Existing profiles are neither deleted nor reset. They remain available to seed live calculations and to produce a manually requested pre-race recommendation. The automatic starting-load option is disabled because it requires history generation to remain enabled.

Automatic starting fuel or VE depends on history generation. Disabling history therefore clears and disables **Automatically apply starting fuel or VE**. Re-enabling history makes the automatic option available again, but does not turn it on automatically.

There is currently no delete or reset button for an individual profile in the plugin interface. To preserve learned data, changing settings and updating the plugin do not erase the history file.

The history file is stored at:

```text
%LOCALAPPDATA%\SimHub\LMUPitCompanion\consumption-history.json
```

## Dashboard and Stream Deck properties

Every property below uses the `LMUPitCompanionPlugin.` prefix. For example, use `LMUPitCompanionPlugin.GeneralMenuTarget` in SimHub. A value can be empty or unavailable while LMU is not running, the relevant identity has not been confirmed, or the calculation does not yet have enough input data.

### Live strategy and actions

| Property | Meaning |
| --- | --- |
| `LastResult` | Result of the latest fuel or VE strategy action. |
| `LastTireResult` | Result of the latest tyre action. |
| `AutoNoRefuelEnabled` | `1` when automatic no-refuel on a pit request is enabled. |
| `PitStopRequested` | Player request signal: `1` requested, `0` clear, `-1` unavailable. |
| `AutoNoRefuelStatus` | Latest automatic pit-request decision or result. |
| `HasVE` | `1` after Virtual Energy is detected in the current session. |
| `LapsToEnd` | Estimated laps remaining, including the current lap. |
| `VEPerLap` | Average Virtual Energy percentage points consumed per lap. |
| `VEPerLapSource` | `HISTORICAL`, `LIVE`, or `UNAVAILABLE`. |
| `VEToEnd` | Full-race Virtual Energy target including reserve; it may exceed 100 for a multi-stop race. |
| `VEReliable` | `1` when enough live VE samples exist. |
| `FuelPerLap` | Average fuel consumption in litres per lap. |
| `FuelPerLapSource` | `HISTORICAL`, `LIVE`, or `UNAVAILABLE`. |
| `FuelToEnd` | Full-race fuel target including reserve; it may exceed tank capacity. |
| `FuelReliable` | `1` when enough live fuel samples exist. |
| `ActiveMode` | Active strategy mode: `VE` or `FUEL`. |
| `ActiveUnit` | Unit for active values: `%` or `L`. |
| `ActiveReliable` | `1` when the active mode has enough live samples. Historical seeding alone does not make live automation reliable. |
| `ActiveSamples` | Accepted live samples for the active mode. |
| `ActivePerLap` | Average consumption per lap for the active mode. |
| `ActiveConfidence` | Confidence label based on the accepted live sample count. |
| `ActiveCurrent` | Current VE percentage or fuel litres. |
| `ActiveTarget` | Full-race general target. |
| `ThisPitTarget` | Full-race target calculated for a stop at the end of the current lap. |
| `GeneralAmountToAdd` | Estimated amount added by the general strategy. |
| `ThisPitAmountToAdd` | Estimated amount added by the this-pit strategy. |
| `GeneralMenuTarget` | Value the general action will actually request: litres to add in FUEL mode or absolute percentage in VE mode. |
| `ThisPitMenuTarget` | Value the this-pit action will actually request: litres to add in FUEL mode or absolute percentage in VE mode. |
| `StrategyStatus` | Unified live-strategy state. |

For a compact Stream Deck page, the most useful combination is `ActiveMode`, `ActiveReliable`, `ActiveCurrent`, `ActivePerLap`, `ActiveTarget`, `GeneralMenuTarget`, `ThisPitMenuTarget`, `ActiveUnit`, `StrategyStatus`, and `LastResult`.

### Pre-race session, estimate, and application

| Property | Meaning |
| --- | --- |
| `PreRaceSessionStatus` | State of the race-duration or lap-count capture. |
| `PreRaceRaceMode` | `TIMED`, `LAPS`, or `UNAVAILABLE`. |
| `PreRaceSessionTimeLeft` | Current SimHub session time remaining in seconds. |
| `PreRaceDuration` | Maximum captured pre-race duration in seconds. |
| `PreRaceTotalLaps` | Captured total lap count for a lap-limited race. |
| `PreRaceRemainingLaps` | Current remaining lap count reported by SimHub. |
| `PreRaceCaptureOpen` | `1` while pre-race session data can still be captured. |
| `PreRaceCapturedUtc` | UTC timestamp of the latest capture update. |
| `PreRaceEnergyMode` | Garage-detected starting-load mode: `VE`, `FUEL`, or `UNAVAILABLE`. |
| `PreRaceEnergyModeStatus` | Garage capability-detection state. |
| `PreRaceGarageProperty` | LMU garage property selected for the starting load. |
| `PreRaceGarageMinValue` | Minimum accepted garage index. |
| `PreRaceGarageMaxValueExclusive` | Exclusive upper limit of the garage index. |
| `PreRaceGarageCurrentValue` | Current garage index for the selected property. |
| `PreRaceGarageDisplayValue` | Current display value returned by LMU. |
| `PreRaceLapTime` | Reference lap time used by the estimate, in seconds. |
| `PreRaceLapTimeSource` | `NATIVE_ESTIMATE`, `ALL_TIME_BEST`, `NOT_REQUIRED`, or `UNAVAILABLE`. |
| `PreRaceEstimatedLaps` | Estimated race laps, including timed-boundary protection. |
| `PreRaceConsumptionPerLap` | Historical consumption used by the estimate. |
| `PreRaceConsumptionSource` | Source of the pre-race consumption value. |
| `PreRaceTarget` | Recommended starting percentage or litres including margins. |
| `PreRaceCurrentValue` | Current comparable starting-load value when available. |
| `PreRaceAmountToChange` | Difference between the recommendation and current value. |
| `PreRaceEstimateUnit` | `%` or `L`. |
| `PreRaceEstimateStatus` | Readiness or exact blocking reason for the estimate. |
| `PreRaceFuelMappingSource` | Source of the fuel-index calibration. |
| `PreRaceTargetGarageIndex` | Garage index corresponding to the target. |
| `AutoPreRaceEnabled` | `1` when automatic starting-load application is enabled. |
| `AutoPreRaceStatus` | Gate state for automatic starting-load application. |
| `PreRaceApplyStatus` | Result of the latest manual or automatic starting-load action. |
| `PreRaceAppliedTarget` | VE percentage or fuel litres requested by that action. |
| `PreRaceVerifiedValue` | VE percentage or fuel litres read back after the action. |
| `PreRaceAppliedGarageIndex` | Garage index requested by the action. |
| `PreRaceVerifiedGarageIndex` | Garage index verified after the action. |

### Consumption-history profile

| Property | Meaning |
| --- | --- |
| `HistoryStatus` | History state for the current car and circuit layout. It reports profile availability, not whether recording is enabled in settings. |
| `HistoryProfileKey` | Normalized physical-car and circuit-layout key. |
| `HistoryVehicleModel` | Resolved physical LMU vehicle model. |
| `HistoryVehicleModelSource` | Source used to resolve the vehicle model. |
| `HistoryVehiclePackage` | Installed LMU vehicle package used by the profile. |
| `HistoryTrackVariant` | Resolved circuit-layout key. |
| `HistoryTrackVariantSource` | Source used to resolve the circuit layout. |
| `HistoryFuelSamples` | Accepted historical fuel samples. |
| `HistoryFuelMean` | Long-term mean fuel consumption in litres per lap. |
| `HistoryFuelRecentMean` | Recent-weighted fuel consumption in litres per lap. |
| `HistoryFuelStdDev` | Standard deviation of historical fuel samples. |
| `HistoryVESamples` | Accepted historical VE samples. |
| `HistoryVEMean` | Long-term mean VE percentage points per lap. |
| `HistoryVERecentMean` | Recent-weighted VE consumption per lap. |
| `HistoryVEStdDev` | Standard deviation of historical VE samples. |
| `HistoryLastUpdatedUtc` | UTC timestamp of the latest accepted sample. |

## Common statuses

### Live strategy and actions

| Status | Meaning |
| --- | --- |
| `WARMUP` | More representative laps are needed. |
| `READY` | The strategy is available and reliable. |
| `NOT_RELIABLE` | The selected action needs more valid samples. |
| `TARGET_UNAVAILABLE` | Current telemetry cannot produce a target. |
| `MULTI_STOP_REQUIRED` | The total VE demand exceeds one full charge. Strategy actions remain available and select the maximum menu value for the next stint. |
| `TARGET_OUT_OF_RANGE` | The calculated target is invalid or unavailable in the Pit Menu. |
| `FINAL_LAP` | The race is on its calculated final lap. |
| `FINISHED` | Finish, retirement, or disqualification has ended strategy calculation and actions. |
| `NOT_RUNNING` | LMU is not providing an active session. |
| `WORKING` | A Pit Menu operation is in progress. |
| `APPLIED` | The change was applied and verified. |
| `BUSY` | Another operation is already running. |
| `AUTO_CANCELLED*` | An automatic operation was cancelled because its telemetry or context was no longer valid. |
| `ERROR_*` | The operation failed or could not be verified. |

### Consumption history

| `HistoryStatus` value | Meaning |
| --- | --- |
| `WAITING_FOR_LMU` | Waiting for LMU telemetry. |
| `NOT_LMU` | The active SimHub game is not Le Mans Ultimate. |
| `NO_IDENTITY` | A trusted physical vehicle model or garage-confirmed circuit variant is missing. |
| `NO_HISTORY` | Identity is valid, but no profile exists for this combination yet. |
| `LEARNING` | The profile exists but does not yet contain an accepted fuel or VE sample. |
| `AVAILABLE` | The profile contains at least one historical consumption value. The pre-race estimate can still report `INSUFFICIENT_HISTORY` until the configured minimum is reached. |

`HistoryStatus` describes the selected profile. It can remain `AVAILABLE` after **Generate consumption history** is switched off because disabling recording does not delete existing data.

### Pre-race estimate

| `PreRaceEstimateStatus` value | Meaning |
| --- | --- |
| `WAITING_FOR_SESSION` | Race duration or lap count is not available yet. |
| `WAITING_FOR_GARAGE_MODE` | The LMU garage has not exposed a supported fuel or VE control. |
| `WAITING_FOR_HISTORY` | No usable historical consumption exists for the selected profile and mode. |
| `INSUFFICIENT_HISTORY` | A historical value exists but has too few accepted samples. |
| `WAITING_FOR_LAP_TIME` | A timed race needs a valid native estimate or all-time best lap. |
| `INVALID_ESTIMATE` | The captured inputs produced a non-finite or invalid estimate. |
| `READY_MAPPING_PENDING` | A fuel estimate exists while the garage fuel-index mapping is still being calibrated. |
| `FUEL_MAPPING_MISMATCH` | The cached fuel mapping does not match the current garage control. |
| `TARGET_OUT_OF_RANGE` | LMU cannot represent the required starting load. |
| `READY` | The recommendation is complete and can be applied. |

### Automatic starting load

| `AutoPreRaceStatus` value | Meaning |
| --- | --- |
| `DISABLED` | Automatic starting load is off. |
| `WAITING_FOR_GARAGE` | Waiting for a clean and supported race-garage state. |
| `WAITING_FOR_ESTIMATE` | Waiting for `PreRaceEstimateStatus = READY`. |
| `WAITING_FOR_GARAGE_SETTLE` | A valid recommendation is being held stable before the one automatic attempt. |
| `ATTEMPTED` | The automatic attempt has already run; inspect `PreRaceApplyStatus` for its result. |
| `RACE_LOCKED` | Track activity or a later pit return has locked further automatic starting-load writes for this race. |

### Pit-request automation

Typical `AutoNoRefuelStatus` values include `DISABLED`, `ARMED`, `REQUESTED`, `SUFFICIENT`, `INSUFFICIENT`, `SKIPPED_MANUAL_OVERRIDE`, `SKIPPED_BUSY`, `SIGNAL_UNAVAILABLE`, and `AUTO_CANCELLED_*`. The status reflects the last decision; always use `LastResult` to display the result of a strategy action itself.

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

The current log rotates at approximately 5 MB to `LMUPitCompanion.1.log`; only one rotated file is retained. Disabling the option stops future writes but does not delete existing log files.

Useful records include:

- `PLUGIN_START`: plugin version, detected SimHub runtime version, and whether diagnostics started successfully.
- `LAP`: accepted or rejected lap time and consumption samples, including the rejection reason.
- `HISTORY_IDENTITY`, `HISTORY_LAP`, and `HISTORY_SAVED`: profile identity and historical-sample lifecycle.
- `PRE_RACE_SESSION`, `PRE_RACE_ESTIMATE`, and `PRE_RACE_APPLY*`: inputs and outcome of a starting-load recommendation.
- `TARGET_AUDIT`: the general and this-pit inputs used immediately before a strategy action.
- `CALCULATION_FAILURE`: a focused snapshot explaining why a manual or automatic calculation could not proceed.

Raw SimHub properties are collected only when a calculation failure needs that additional context; the plugin does not continuously log every raw property. Review the log before attaching it to a public issue because it can contain car, track, session, setup, and telemetry details.

## Important limitation

The strategy is an estimate based on recent consumption. Weather, traffic, safety-car periods, damage, driving style, and pace changes can affect the real requirement. The driver remains responsible for the final pit-stop settings.
