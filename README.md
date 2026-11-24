![Logo](admin/philips-air.png)
# ioBroker.philips-air-cloud

[![Test and Release](https://github.com/reloxx13/ioBroker.philips-air-cloud/actions/workflows/test-and-release.yml/badge.svg)](https://github.com/reloxx13/ioBroker.philips-air-cloud/actions/workflows/test-and-release.yml)

Fork of [ioBroker.philips-air](https://github.com/iobroker-community-adapters/ioBroker.philips-air).

This fork uses the adapter ID `philips-air-cloud`. Its instances use their own namespace, for example `philips-air-cloud.0`. Existing `philips-air` instances and their settings are separate.

Connects Philips air purifiers and selected Philips/Versuni fans with ioBroker.
**Tested with AC2729 and the Philips/Versuni fans CX3550/01 and CX7550/01**, but should work with newer purifiers that communicate via local CoAP with encryption.
<img src="img/device.png" alt="Philips AC2729 air purifier" width="180">

[Link to philips website](https://www.philips.de/c-m-ho/luftreiniger-und-luftbefeuchter/kombi)

## Supported devices

### Local HTTP / CoAP

Confirmed on real hardware: AC2729, AC2889, AC3221, AC3829, AC4236/14,
CX3550/01 and CX7550/01. See [model selection](#which-device-model-should-i-select)
for the matching configuration. Other classic purifiers can use the shared
`AC2889` mapping; unlisted local devices start with read-only `Generic`.

### Philips Cloud (Air+ account)

Cloud support is experimental and uses an unofficial Philips/Versuni API. Provider changes to the
API or app credentials can interrupt the connection or require an adapter update.

One Cloud instance automatically connects all devices returned by the account.
Each device has its own object tree under `devices.<deviceId>`, including its
status, controls and connection indicator. Profiles are selected automatically
from the model number; no device or model selection is needed.

The Cloud connection includes profiles for these models:

| Device / family | Modes |
| --- | --- |
| AC0650 | Gentle, Sleep, Turbo |
| AC0651 | Auto, Gentle, Sleep, Turbo |
| AC17xx, including AC1715 | Auto, Speed 1, Speed 2, Sleep, Turbo |
| AC0710, AC0720, AC0721 | Gentle, Sleep, Turbo |

Cloud operation has been confirmed for AC0651. The other listed models have
not yet been tested with this adapter. Individual controls and filter resets
still need confirmation on real devices.

Other models returned by the account are added automatically, but compatibility
is not guaranteed. Available readings and controls depend on the device.
See [Cloud setup and limits](docs/cloud.md).

## Usage
For an Air+ account connection, select **Philips Cloud** and follow the
[Cloud setup and limits](docs/cloud.md). Sign in
by entering your account email and the verification code in the adapter settings.
No account password is required.

Cloud supports multiple account devices in one instance. With **HTTP or CoAP**,
each instance connects **one local device**; create another instance for another
device.

For a local HTTP or CoAP connection:
Enter the IP address or the hostname of your device. You can find it in your router, where the device often shows up as `MiCO`.
New instances default to Philips Cloud. For a local connection, select CoAP for most devices or HTTP for older devices such as the AC2729 and AC3829.
Then pick your device model, so that the adapter creates the controls that match your device. A new instance starts with `Generic`: you get every read-only value, but no controls until you select a model. `Generic` is also the right choice if your model is not in the list.
It can happen that a device does not report all variables; those stay unfilled in the object tree. Raw values the adapter does not recognise are collected under `unknownStates`.

### The two timing settings

Both are in milliseconds and rarely need changing.

| Setting | Default | What it does |
| --- | --- | --- |
| Alive timeout | 30000 | How long a single request to the device may take before it is given up. Over HTTP it is also the polling interval. |
| Reconnect interval | 30000 | How long to wait before the first retry after a failed connection. Further failures double the wait, up to five minutes, so an unreachable device is not hammered. It must not be shorter than the alive timeout. |

Over CoAP the device pushes its status on its own, so there is no polling. Some devices - the CX7550/01 for example - can stay silent for hours; the adapter then checks the connection by asking the device directly instead of rebuilding it.

### Which device model should I select?

This selection applies to local HTTP / CoAP connections. Cloud profiles are selected automatically.

| Your device | Model to select |
| --- | --- |
| AC2889 and the other classic purifiers, for example AC1214, AC2729, AC2939, AC3059, AC3829 or AC4236 | `AC2889` |
| AC3221 | `AC3221` |
| AC3737/10 2-in-1 purifier/humidifier | `AC3737` |
| CX3550/01 pedestal fan | `CX3550` |
| CX7550/01 tower fan | `CX7550` |
| Anything else, or if you are unsure | `Generic` |

The classic purifiers all report the same plain keys (`pwr`, `om`, `mode` and so on), which is why one entry covers the whole family. Confirmed on real hardware so far: AC2729, AC2889, AC3221, AC3829, AC4236/14, CX3550/01 and CX7550/01. The `AC3737` entry was built from a complete status frame of an AC3737/10 (GitHub #407) and then confirmed on that device: the readings match the app, and writing the mode and the humidity target from ioBroker were both followed by the device. Two notes specific to this 2-in-1: all three filter pairs are assigned, but not the way the other new-gen models spell them - the 4800 h pair is the humidifier (wick) filter here and the 2400 h pair is the air filter (`hepaFilter*`), both confirmed against the percentages the app shows. And `status.waterTankPresent` really means "tank seated": with the tank in but empty it stays `true`, while `device.error` switches to `no water`.

The model number on its own tells you nothing about the register set: the AC4236/14 carries a higher number than the AC3221, yet it is a classic device and needs `AC2889`. Pick the entry that matches the keys your device reports, not the one that looks closest to its name.

If you are unsure, connect with `Generic` first and look at the raw keys under `unknownStates`: plain names such as `pwr` or `pm25` mean a classic device, keys such as `D03102` mean a next-generation device. If your device turns out to be a next-generation model that is not in the list, please open an issue with a debug log - that is how the CX7550/01 and the AC3221 were added.

Developer notes on the app's connection families, handshakes and the AC0651
remote-control path are in [Protocol reference](https://github.com/reloxx13/ioBroker.philips-air-cloud/blob/master/docs/protocols/README.md).

![Objects](img/objects.png)

## Philips/Versuni fans (local CoAP)

Use one local instance per fan and select its exact model. Both models support
power, sleep, natural breeze, oscillation and beep, but their raw command values
differ.

| Device | Model setting | Main differences | Detailed guide |
| --- | --- | --- | --- |
| CX3550/01 pedestal fan | `CX3550` | Speeds 1–3; timer read-only | [Setup, controls and protocol](docs/devices/cx3550.md) |
| CX7550/01 tower fan | `CX7550` | Speeds 1–12, AutoAdapt, writable timer, display and temperature | [Setup, controls and protocol](docs/devices/cx7550.md) |

**CX3550 timer warning:** local timer writes can switch the fan off. The adapter
therefore exposes its timer values read-only. CX7550 timer control is supported.

Further setup instructions and technical references: [Documentation](docs/README.md).

## Changelog
### 2.5.4 (2026-11-24)
- (reloxx13) Corrected the roles for string-valued control options.

### 2.5.3 (2026-11-24)
- (reloxx13) Corrected the Cloud device folder and roles for the device start time and reported fan speed.
- (reloxx13) Encoded unexpected local device attributes as single state ID segments.

### 2.5.2 (2026-11-24)
- (reloxx13) New instances default to Philips Cloud.
- (reloxx13) Fixed a verification button that could keep loading after successful sign-in.

### 2.5.0 (2026-11-24)
- (reloxx13) Added experimental Philips Cloud support with login version diagnostics and automatic network recovery

<!--
    Placeholder for the next version (at the beginning of the line):
    ### **WORK IN PROGRESS**
-->

[Older releases and upstream history](CHANGELOG_OLD.md)

## License
MIT License


Copyright (c) 2023-2026 iobroker-community-adapters <iobroker-community-adapters@gmx.de>  
Copyright (c) 2020-2022 ioBroker <dogafox@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
