# LMU Pit Companion

**Smarter pit stops for Le Mans Ultimate**

[![Latest release](https://img.shields.io/github/v/release/valenjimeno/lmu-pit-companion?display_name=tag&sort=semver)](https://github.com/valenjimeno/lmu-pit-companion/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/valenjimeno/lmu-pit-companion/total)](https://github.com/valenjimeno/lmu-pit-companion/releases)
[![Buy Me a Coffee](https://img.shields.io/badge/Buy_Me_a_Coffee-Support_the_project-FFDD00?logo=buymeacoffee&logoColor=000)](https://www.buymeacoffee.com/YOUR_USERNAME)

LMU Pit Companion is a SimHub plugin that estimates the fuel or Virtual Energy needed to finish a race and helps configure the Le Mans Ultimate Pit Menu from a steering wheel, button box, Stream Deck, keyboard, or any other controller supported by SimHub.

> LMU Pit Companion is currently in public beta. Always verify the in-game Pit Menu before entering the pits.

<!-- Add a short GIF or screenshot here before publication. -->

## Highlights

- Calculates fuel and Virtual Energy targets from recent representative laps.
- Provides a dedicated target for stopping at the end of the current lap.
- Detects when the car already has enough energy to finish.
- Can optionally avoid unnecessary refuelling after a pit request.
- Can optionally apply the recommended target after a pit request.
- Includes actions for no refuelling and common tyre-change selections.
- Exposes SimHub properties for dashboards, overlays, and controller feedback.
- Offers an English and Spanish settings interface.
- Verifies Pit Menu changes and attempts a rollback if an operation fails.

## Requirements

- Windows
- Le Mans Ultimate
- SimHub

Virtual Energy is read directly from LMU telemetry exposed by SimHub. No additional telemetry plugin is required.

## Installation

1. Download the latest ZIP from [GitHub Releases](https://github.com/valenjimeno/lmu-pit-companion/releases/latest).
2. Close SimHub.
3. Extract the ZIP.
4. Copy `LMUPitCompanionPlugin.dll` into the SimHub installation folder, normally `C:\Program Files (x86)\SimHub\`.
5. Start SimHub and enable **LMU Pit Companion** if prompted.
6. Open the plugin from the SimHub sidebar, assign the actions you want, and select **Save and apply**.

To update, close SimHub and replace the existing DLL with the newer version. Your settings are retained by SimHub.

## First pit stop

1. Start SimHub before starting or joining an LMU session.
2. Complete enough representative laps for `StrategyStatus` to become `READY`.
3. Request a pit stop in LMU.
4. Run `LMUPitCompanionPlugin.ApplyStrategyThisPit`, or enable automatic application in the plugin settings.
5. Verify the LMU Pit Menu before entering the pits.

The calculated value is the total fuel or Virtual Energy target after the stop, not a fixed amount to add.

## SimHub actions

| Action | Purpose |
| --- | --- |
| `LMUPitCompanionPlugin.ApplyStrategy` | Apply the conservative finish target. |
| `LMUPitCompanionPlugin.ApplyStrategyThisPit` | Apply the target for stopping this lap. |
| `LMUPitCompanionPlugin.NoRefuel` | Select the minimum fuel or Virtual Energy setting. |
| `LMUPitCompanionPlugin.TiresNoChange` | Cancel all tyre changes. |
| `LMUPitCompanionPlugin.TiresAllCurrent` | Change all tyres using their current compounds. |
| `LMUPitCompanionPlugin.TiresFrontCurrent` | Change only the front tyres. |
| `LMUPitCompanionPlugin.TiresRearCurrent` | Change only the rear tyres. |
| `LMUPitCompanionPlugin.TiresAllWet` | Change all four tyres to wets. |

## Documentation and support

- [User guide](user-manuals/USER_GUIDE.md)
- [Release history](CHANGELOG.md)
- [Getting help and reporting bugs](SUPPORT.md)
- [Security policy](SECURITY.md)

## Support the project

LMU Pit Companion is developed and maintained independently. If it improves your races and you would like to support its continued development, you can buy me a coffee:

[![Buy Me a Coffee](https://img.shields.io/badge/Buy_Me_a_Coffee-Support_LMU_Pit_Companion-FFDD00?logo=buymeacoffee&logoColor=000)](https://www.buymeacoffee.com/YOUR_USERNAME)

Support is optional and does not unlock features or receive priority support.

## License

LMU Pit Companion is distributed as proprietary binary software for personal use. Redistribution, modification, decompilation, and commercial use are not permitted without prior written permission. See the [license](LICENSE.md) for the complete terms.

## Disclaimer

LMU Pit Companion is an independent community project. It is not affiliated with or endorsed by Studio 397, Motorsport Games, Le Mans Ultimate, or SimHub. The strategy is an estimate based on available telemetry; the driver remains responsible for the final pit-stop settings.
