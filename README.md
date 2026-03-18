# ZMK config for beekeeb Toucan Keyboard

[The beekeeb Toucan Keyboard](https://beekeeb.com/toucan-keyboard/) is a wireless split keyboard with a display and a trackpad, with an aggressive stagger on the pinky columns.

## Layout

Each half has **5 columns × 3 rows** of finger keys plus **3 thumb keys**, giving **18 keys per side** and **36 keys total**.

## Building & Flashing

### GitHub Actions builds

Pushing to this repository triggers a GitHub Actions build that produces three firmware artifacts:

| Artifact | Description |
|---|---|
| `toucan36_left` | Left half firmware (central controller — holds the keymap, display, and trackpad) |
| `toucan36_right` | Right half firmware (peripheral — handles key scanning only) |
| `settings_reset` | Clears the stored BLE pairing data; flash to either half to fix pairing issues |

### Do I need to flash both sides when I change the keymap?

**No — only flash the left side.**

In a ZMK split keyboard the keymap is stored exclusively on the central (left) half.  The right half is a peripheral: it scans its key matrix and sends raw key events to the left over BLE, but it does not contain or interpret the keymap.

| Change | Left | Right |
|---|---|---|
| Keymap (`.keymap` file) | ✅ Flash | ❌ Not needed |
| ZMK firmware version | ✅ Flash | ✅ Flash |
| Right-side hardware config (overlay/dtsi) | ❌ Not needed | ✅ Flash |
| Left-side hardware config (overlay/dtsi) | ✅ Flash | ❌ Not needed |

### First-time setup (pairing the two halves)

1. Flash `settings_reset` to **both** halves and let them reboot.
2. Flash `toucan36_right` to the right half.
3. Flash `toucan36_left` to the left half.
4. The two halves will pair automatically over BLE within a few seconds.

# License

The code in this repo is available under the MIT license.

The included shield nice_view_gem is modified from https://github.com/M165437/nice-view-gem licensed under the MIT License.

ZMK code snippets are taken from the ZMK documentation under the MIT license.

The embedded font QuinqueFive is designed by GGBotNet, licensed under under the SIL Open Font License, Version 1.1.
