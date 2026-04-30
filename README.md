# ZMK config for beekeeb Toucan & GEIST Totem

This repo builds firmware for two keyboards from a shared keymap:

- **Toucan** (main branch) — [beekeeb Toucan](https://beekeeb.com/toucan-keyboard/), wireless split with display + trackpad.
- **Totem** (`totem` branch) — [GEIST TOTEM](https://github.com/GEIGEIGEIST/TOTEM) (38-key column-staggered split, XIAO BLE per side) paired with a [beekeeb Prospector](https://shop.beekeeb.com/products/zmk-wireless-dongle-prospector-diy-kit) dongle as the central. The two outer-pinky `SW16` keys are intentionally unmapped so the layout matches Toucan's 36 keys.

The Toucan keymap (`config/toucan36.keymap`) is the only keymap in `config/` and the single source of truth — including for the Totem build. The keymap editor (and any tool that scans `config/`) sees only `toucan36`. See [Keymap sharing](#keymap-sharing) below.

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

## Totem build artifacts (totem branch)

| Artifact | Description |
|---|---|
| `totem_dongle` | Prospector dongle firmware (central; holds keymap + LCD) |
| `totem_left` | Left half firmware (peripheral, key scanning only) |
| `totem_right` | Right half firmware (peripheral, key scanning only) |

Pair the dongle with the left half first, then the right half (left-to-right pairing order is what the Prospector battery widget uses).

## Keymap sharing

`config/toucan36.keymap` is the only keymap file in `config/`. The totem build picks it up at compile time via a one-line shim:

```c
// boards/shields/totem/totem.keymap
#include "../../../config/toucan36.keymap"
```

So:

- The keymap editor (and any tool that scans `config/`) sees only `toucan36`.
- `git push` triggers the GitHub Actions build, which compiles totem firmware from the same source via the shim — no copy step, no pre-commit hook, nothing to install after `git clone`.
- Totem's matrix transform omits the two outer-pinky `SW16` positions, so they remain empty regardless of what the keymap contains. Whatever you bind in `toucan36.keymap` lands on the 36 shared positions; SW16 stays unmapped.

# License

The code in this repo is available under the MIT license.

The included shield nice_view_gem is modified from https://github.com/M165437/nice-view-gem licensed under the MIT License.

ZMK code snippets are taken from the ZMK documentation under the MIT license.

The embedded font QuinqueFive is designed by GGBotNet, licensed under under the SIL Open Font License, Version 1.1.
