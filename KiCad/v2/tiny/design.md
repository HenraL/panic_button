# Design notes — panic_button_tiny (KiCad v2, "tiny" variant)

Working document for ideas, decisions, and open questions around the circuit. This is the
"sorting bin" — the strict bill of materials lives in `bom.md`.

## Goal

A **panic beacon** — small device (target: no bigger than a finger; a palm if that size can't
be reached). General-purpose; the current `tiny` variant happens to target a cat collar
(user's use case), but the design is not tied to that form factor.

Same family as the earlier v1/v2 dev designs, shrunk as far as practical.

## Tiny vs dev

- **Tiny** = the smallest possible circuit, as arranged in `panic_button_tiny/`.
- **Dev** = the tiny circuit + test infrastructure: test points, a testing array of buttons,
  richer debug status LEDs, and an **AirM2M Core ESP32-C3 module** as the easier-to-mount C3
  (same ESP32-C3FH4 die as the tiny's bare QFN, castellated module, onboard USB-C/buttons/RGB
  LED — Arduino framework supported). Functionally identical, same
  BOM family — any part dropped in tiny for footprint reasons is dropped in dev too. Dev
  extras only make the circuit easier to probe/debug; they never change what the circuit does.

## Hard functional spec (both variants)

1. Update the beacon's position towards a server.
2. If that is not available, send an SMS (or similar).
3. Regardless of the above, advertise BLE so phones can interact without an app
   (Apple Find My-style, Android hub, Samsung if they deviate).

Concretely the modem must provide: **a GNSS position fix, SMS, and HTTP/HTTPS + MQTT
(or similar) over LTE, all via AT commands**. Any modem substitution must keep all three.

## Decisions made

- **No UWB.** Apple UWB requires Apple's proprietary U1/U2 chip + MFi program — not reachable
  from an ESP32. Phone interaction relies on **BLE beacons only**:
  - Apple: Find My network accessory profile (BLE, keys-based, app-less).
  - Android: Find My Device hub (BLE).
  - Samsung: SmartThings Find (BLE, if they deviate from Android's hub).
- **Modem: SIM7670G** (24×24 mm) — the realistic floor for "Cat1 + GNSS + SMS + HTTP/MQTT in
  one part". Smaller Cat1 modules (EC200U, L610) are the same size class without GNSS; going
  smaller means LTE-M (loses SMS guarantees + needs a separate GNSS chip). Unless the
  footprint ceiling demands it, SIM7670G stays.
- **Position** comes from the SIM7670G's built-in GNSS (the `G` variant), with LTE cell
  triangulation as a fallback → POST to the server via HTTP(S) over LTE.
- **"If movement"** requires a MEMS accelerometer with a wake-on-motion interrupt to wake the
  MCU from deep sleep — neither the ESP32-C3 nor the modem can sense motion.

## Firmware (PlatformIO)

- **One shared codebase** in `platformio/` — the same firmware for whatever PCB it ends up on.
- The `board = ...` declaration in `platformio.ini` acts mainly as a **RAM/flash + build-flag
  reference** (e.g. an ESP32-C3 board def) — it is not tied to our physical boards or their
  components.
- Less desirable alternative, still available: define **our own board entry** in the PIO
  config (custom memory/flags) instead of borrowing a stock one.

## Power / sleep notes

- SIM7670G supply range 3.4–4.3 V (typ. 4.0 V). A bare Li-ion (3.2–4.2 V) can sag under TX
  bursts — plan a steady 4 V rail (boost) or a fat bulk capacitor, plus a load switch so the
  modem draws nothing in deep sleep.
- Deep-sleep modem-off budget should let the beacon sit for weeks; wake only on motion
  interrupt, panic button, or periodic heart-beat timer.
- Each position report is a ~2 A burst for a few seconds — bulk caps on the modem rail matter
  as much as the cell.

## Open questions

- Footprint ceiling for the cat: is a flat ~30×24 mm collar puck acceptable, or must the
  design fight for something smaller (which re-opens the modem question)?
- Report cadence when moving (sets cell size + charging expectations).
- One LED or none on the tiny version?
- Boot/reset as test pads instead of buttons on the tiny version (no switches on the cat)?
- Waterproofing strategy (sealed USB-C, charging contacts, enclosure) — shapes the final
  form factor.