# LMU Pit Companion v0.32.2

This corrective release fixes automatic Pit Menu handling when LMU starts a race with a stable fuel or Virtual Energy value that differs from the protocol's reported default.

## Fixed

- Automatic no-refuelling now accepts a stable session selection, such as a 70% starting Virtual Energy value, instead of misclassifying it as a manual override because LMU reports 100% as the protocol default.
- Automatic this-pit strategy application uses the same corrected behavior.
- A real fuel or Virtual Energy selection change between the two safety reads still cancels the automatic write.

## Installation

1. Close SimHub.
2. Extract `LMUPitCompanion-0.32.2.zip`.
3. Replace `LMUPitCompanionPlugin.dll` in `C:\Program Files (x86)\SimHub\`.
4. Start SimHub and verify the desired actions remain assigned.

Existing settings and consumption history are retained. Always verify the in-game Pit Menu before entering the pits.

## Verification

- Plugin version: `0.32.2.0`
- Automated test suite: 1,348 assertions passed
- Release build: zero compiler warnings and zero compiler errors

The SHA-256 is published as the separate `LMUPitCompanion-0.32.2.zip.sha256` release asset.
