# Changelog

This file records public releases of LMU Pit Companion.

## [Unreleased]

### Documentation

- Expanded the user guide with the complete consumption-history lifecycle, accepted and rejected sample rules, setting dependencies, pre-race and history statuses, diagnostic behavior, and a full reference for every public SimHub property.
- Clarified the exact meaning of the general and this-pit menu targets for conventional fuel and Virtual Energy, including values shown on Stream Deck dashboards.

## [0.31.0] - 2026-10-18

### Fixed

- Automatic pre-race loading no longer treats stale practice or qualifying pit flags as proof that the car has already entered the race track. It waits for a clean race-garage frame while retaining the real on-track and pit-return locks.
- Virtual Energy targets above 100% are treated as valid multi-stop race demand. General, this-pit and automatic this-pit actions now select the largest available VE setting for the next stint instead of failing with `ERROR_TARGET_OUT_OF_RANGE`.
- Automatic this-pit strategy evaluates its own target instead of being rejected when the general target exceeds Virtual Energy capacity.
- Native player finish status ends the strategy immediately after finish, retirement or disqualification, even when SimHub's global checkered flag remains clear.

### Added

- Active strategy sample count, per-lap consumption, confidence, amounts to add and the general/this-pit menu targets are published as SimHub properties for dashboards and Stream Deck profiles.
- A settings option to disable new consumption-history recording. Turning it off also clears and disables automatic pre-race fuel or VE application; existing history is preserved.
- Plugin and SimHub versions in startup logs, plus focused failure diagnostics for manual and automatic strategy calculations.

## [0.30.0] - 2026-10-01

### Added

- Optional automatic pre-race fuel or Virtual Energy loading, enabled from the Automation section with Save and apply; disabled by default.
- A single automatic attempt after the garage recommendation has remained stable for three seconds, with a race lock that survives telemetry interruptions and session GUID changes.
- Automatic re-arming after a garage-confirmed change of vehicle model or circuit layout, or an observed transition through another session type.
- AutoPreRaceEnabled and AutoPreRaceStatus dashboard properties.

### Fixed

- Garage identification can finish after SimHub's transient menu flag clears while LMU remains in the pre-race garage.

### Usage notes

- Wait for the starting-load operation to finish before joining the track. Immediate departure can retain the setup's original load.
- A new race with the same model and layout may remain locked if no different session type was observed; the manual pre-race action remains available.

## [0.29.5] - 2026-09-30

### Added

- Compact fuel and Virtual Energy consumption history, stored locally per physical LMU vehicle model and exact circuit layout.
- Pre-race estimates for timed and lap-limited races using historical consumption, the configured safety margin, and LMU/SimHub session data.
- `ApplyPreRaceLoad` action to set the recommended starting fuel or Virtual Energy before joining the track.
- Dashboard properties exposing history identity, sample statistics, pre-race inputs, recommendation status, and verified application results.

### Changed

- Historical consumption can seed the live strategy until representative current-session samples become available, without marking live automation as reliable.
- Vehicle and circuit identification is resolved from the LMU garage only when needed and retained for the current logical context to avoid continuous API polling.

### Fixed

- Physical vehicle identity is preserved across menu/session transitions and telemetry retries instead of falling back to team names.
- Provisional history collected on track is migrated to the trusted physical vehicle and circuit-layout profile when the pre-race garage identity becomes available.
- Conventional-fuel and Virtual Energy starting loads are mapped to the correct LMU garage property and verified after application.

## [0.27.1] - 2026-09-28

### Fixed

- Conventional-fuel strategy actions now convert the total finish target into the amount to add in the LMU Pit Menu and choose the maximum available fill when one tank cannot reach the finish.
- Timed-boundary protection now contributes its extra lap to the general fuel and Virtual Energy targets, keeping the general and this-lap plans distinct.
- Current-compound tyre actions now fall back to the first used set of the fitted compound when no new set is available.
- If LMU rejects an advertised new tyre set, the plugin safely restores the menu and retries with a used set of the same compound instead of leaving wets or another unintended selection active.

## [0.27.0] - 2026-09-27

### Added

- Individual current-compound tyre actions for `FL`, `FR`, `RL`, and `RR`.
- Settings UI controls and English/Spanish labels for all four individual-wheel actions.

## [0.26.0] - 2026-09-27

### Added

- `TiresLeftCurrent` action to change the front-left and rear-left tyres using each wheel's fitted compound.
- `TiresRightCurrent` action to change the front-right and rear-right tyres using each wheel's fitted compound.
- Settings UI controls and English/Spanish labels for both side-specific tyre actions.

## [0.25.2] - 2026-09-26

### First Public Release

- Fuel and Virtual Energy strategy calculations based on recent laps.
- General and this-lap pit-stop targets with configurable safety margins.
- Automatic no-refuelling when the car already has enough energy to finish.
- Optional automatic strategy application when a pit stop is requested.
- Eight assignable SimHub actions for strategy, refuelling, and tyre selection.
- SimHub properties for dashboards, overlays, and controller feedback.
- English and Spanish settings interface.
- Pit Menu verification, safe rollback, and optional diagnostics logging.
- Native SimHub Virtual Energy telemetry with no additional telemetry plugin required.
