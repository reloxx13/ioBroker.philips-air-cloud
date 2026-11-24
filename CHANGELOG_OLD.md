# Older changes

## 2.4.1 (2026-11-17)
- (tt-tom17) AC2889 family: the child lock is now written as a boolean, the format the classic devices expect - an AC4236/14 ignored the command before (GitHub #411)

## 2.4.0 (2026-11-06)
- (tt-tom17) New device model "AC3737" for the AC3737/10 2-in-1 purifier/humidifier: readings, all three filter pairs, the humidity target, the water tank state, the "no water" error and the preferred index are now named instead of appearing as raw values; writing the mode and the humidity target is confirmed on the device
- (copilot) Adapter requires node.js >= 22.19.0 now


## 2.3.0 (2026-10-26)

- (tt-tom17) A new instance now starts with the device model "Generic" (read-only values, no controls) instead of silently using the AC2889 controls; instances that already have a model keep it
- (tt-tom17) With the device model "Generic" the log now names the model your device looks like, so you know which one to select
- (tt-tom17) Dependencies updated

## 2.2.0 (2026-09-08)

- (tt-tom17) Added the combined allergen/sleep preset ("Allergie-/Ruhemodus") reported by the AC4236/14 (VMI1)
- (tt-tom17) Classic devices now show the total filter life next to the remaining hours, plus the device language and version (VMI1)
- (tt-tom17) The "wrong device model" warning is no longer silenced by a single register that both models use (VMI1)
- (tt-tom17) Fixed a device attribute spelled like one of the adapter's own state names being treated as a mapped value (VMI1)

## 2.1.0 (2026-08-29)

- (tt-tom17) Fixed error messages ("DB closed", "setTimeout called, but adapter is shutting down") that appeared in the log every time the adapter was stopped or restarted (MatthiasBosch)
- (tt-tom17) New setting "Log unknown device attributes as debug": moves the "Unknown raw device attribute" messages from the info log to the debug log (off by default)
- (tt-tom17) Fixed devices connected via CoAP reconnecting every few minutes, and the log filling with "connection lost / connected" pairs, although the connection was fine - this affected quiet devices such as the CX7550/01 (DrBakterius)
- (tt-tom17) A device that stays unreachable is now retried at growing intervals instead of every 30 seconds, and stops repeating the same error line in the log
- (tt-tom17) No longer suggests switching to CoAP when an HTTP device that was working loses its connection - the hint now only appears while HTTP has never worked (tukey42)

## 2.0.0 (2026-08-23)

- (tt-tom17) New "Device model" setting: pick your model so the adapter shows the correct controls for your device
- (tt-tom17) Added support for the AC3221 next-generation purifier (MatthiasBosch)
- (tt-tom17) Added support for the CX7550/01 tower fan (DrBakterius)
- (tt-tom17) The adapter now warns in the log when the selected model does not seem to match the connected device
- (tt-tom17) Values the adapter does not recognise are collected under "unknownStates"
- (tt-tom17) IMPORTANT: all state IDs starting with "cx" were renamed to generic names (for example "fanMode" instead of "cxFanMode"). Please select your device model once in the settings; the old "cx*" objects can be deleted manually
- (tt-tom17) Fixed switches that did nothing when a script or visualisation wrote them as the text "true"/"false" instead of a real on/off value
- (tt-tom17) Fixed devices connected via HTTP logging "Cannot parse: undefined" every time a command was sent; the device answer is now read correctly
- (tt-tom17) Fixed devices using the HTTP protocol (for example the AC3829 and AC2729) that stopped connecting in version 1.4.0 and only logged "fetch failed (UND_ERR_SOCKET)"; requests are sent the way these devices expect again

## 1.6.1 (2026-07-03)
- (Holly86) Added support for Philips/Versuni CX3550/01 pedestal fan.
- (Holly86) Added CX fan modes, oscillation, beep and read-only timer state.
- (Holly86) Timer control is intentionally not exposed because local timer writes can switch the fan off.

## 1.5.0 (2026-06-24)
- (tt-tom17) CoAP connection now stays stable instead of disconnecting every few minutes
- (tt-tom17) Fixed adapter checker warnings

## 1.4.0 (2026-06-17)
- (tt-tom17) Connection to CoAP and HTTP devices is much more reliable now: several cases that could crash the adapter, freeze the connection or stop it from reconnecting have been fixed
- (tt-tom17) Air quality, filter and on/off values are now shown with the correct type and update reliably
- (tt-tom17) Clearer log messages, including a hint to switch to CoAP when a device does not answer on HTTP
- (tt-tom17) HTTP mode no longer needs the extra "philips-air" package and its outdated dependencies
- (tt-tom17) The device address field now accepts an IP address or a hostname and warns about invalid input
- (tt-tom17) Dependencies updated

## 1.3.0 (2026-06-15)
- (copilot) Adapter requires node.js >= 22 now
- (copilot) Adapter requires admin >= 7.7.22 now
- (mcm1957) Dependencies have been updated

## 1.2.0 (2025-02-10)
* (mcm1957) Adapter requires node.js >= 20, js-controller >= 6 and admin >= 6 now.
* (mcm1957) Adapter migrated to eslint 9 / @iobroker/eslint-config
* (mcm1957) Materialize UI support has been removed
* (mcm1957) jsonConfig responsive design size attributes have been added
* (mcm1957) Dependencies have been updated

## 1.1.0 (2024-04-25)
* (mcm1957) Adapter requires node.js >= 18 and js-controller >= 5 now
* (mcm1957) Dependencies have been updated

## 1.0.3 (2022-12-23)
* (Apollon77) Finalized and optimized HTTP communication protocol
* (Apollon77) Fixed type issues with device.error

## 1.0.2 (2022-11-16)
* (bluefox) Small fixes done
* (bluefox) Added HTTP communication protocol (untested!)
* (mdax82) Added `gentle/GT` for AC2939

## 0.1.7 (2022-05-19)
* (Apollon77) Upgrade coap library

## 0.1.4 (2022-03-23)
* (Apollon77) Downgrade coap library to restore functionality for some devices
* (Apollon77) Prevent crash case and make control more flexible
* (Apollon77) correctly handle `control.function` state

## 0.1.3 (2022-03-12)
* (Apollon77) General updates and optimizations

## 0.1.1 (2020-10-14)
* (bluefox) initial release
