# Changelog

This file records public releases of LMU Pit Companion.

## [Unreleased]

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
