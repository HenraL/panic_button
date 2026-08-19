<!-- 
-- +==== BEGIN AsperHeader =================+
-- LOGO:
--               @@@@@@@@@
--            @@@@       @@@@
--           @@  @@@@@@@@@  @@
--             @@@  @@@  @@@
--               @@@@@@@@@
--               @@ @@@ @@
--                 @@@@@
--                   @
--                  @@@
--                 @- #@
--                 @: @@
--                  @ @
--                  @ @
--                  @ @
--                 -@ @:
--                 =@ @*
--                 ## -#
--               %@@@@%@@@
--               @       @@
--            -@%@@@@#%@@@@@+
--         @@%+             =%@%#
--      =@@.                    @@#
--     %@:                       *@@-
--   *@*                           *%%
--  #@=         .   +*=             +@%
--  @*         :@@@@+-+@@@=          *@
-- %@          @@-        @@%         @@
-- %@         @%  %@@-@@@  #@         @@
-- @+        %@= @@     @@  @@        +@
-- @+        *@- @@     @@ -@%        +@
-- %@         @%  =@@@@@#  #@         @@
-- *@:         @@%       *@@+        :@%
--  @#           @%@*+@@%@#          #@
--  #@%                           . #@*
--    @%                           %@
--     @%@                       @@%
--      -@@%                   #@@=
--         *@@@             @@@#
--             @%@@@@@@@@@@@+
-- /STOP
-- PROJECT: AsperHeader
-- FILE: design.md
-- CREATION DATE: 14-08-2026
-- LAST Modified: 14:21:28 14-08-2026
-- DESCRIPTION:
-- A small panic beacon: reports its position to a server, falls back to SMS when unreachable, and advertises BLE (Find My-style) so phones can interact with it without an app.
-- /STOP
-- COPYRIGHT: (c) Asperguide
-- PURPOSE: This is the file containing the intended design of the circuit and why.
-- // AR
-- +==== END AsperHeader =================+
-->
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
- **BMS is a DW01A-enable toggle (reworked 19-08-2026).** U6 (DW01A) + U7 (8205A) are
  **always populated**; the 8205A FETs are the only cell − → GND path. The toggle is a
  2-pad testpoint switch in the DW01A's GND pin (µA — nothing heats, no power-path switch):
  - Jumper **open** (default — cell with internal protection PCB, typical 603050 pouch):
    DW01A dead (no return path), OD/OC held high by **R12/R13 (470 kΩ) pull-ups to +BATT**
    → both gates on → the FETs conduct like a wire; the battery's own BMS owns the cell.
  - Jumper **bridged** (bare cell, no BMS): DW01A powered (VCC → BT1 (+) pre-switch, so
    protection stays armed while charging with the device off) → overcharge /
    overdischarge / overcurrent protection through the 8205A gates.
  - Silkscreen: "link only if the battery does not have a BMS; otherwise leave open".
- **Reverse-polarity protection: one high-side P-FET (Q1 = Si2305CDS, SOT-23).** The
  DW01A/8205A combo protects the cell, not a reversed plug — a reversed battery forward
  biases every body diode and kills the charger. Q1 (source = SW4 output, drain = +BATT,
  gate = GND) blocks that with zero drop. **Q2 (Si2301CDS) has no role — drop it.**
- **Reverse-powering protection (19-08-2026) — what protects what, and the rule:**
  - **Never series-diode the main rails** (+BATT, 3.3 V) — the 0.3–0.5 V Schottky drop is
    unacceptable in a battery device; that's exactly why Q1 is a FET (zero-drop ideal diode).
  - **Cell:** Q1 covers reversed cell insertion; DW01A/8205A block reverse cell current.
  - **5 V/VBUS (J2):** a series Schottky (SS14/SS24) blocks a reversed or mis-wired USB /
    bench supply — the TP4056 has no internal reverse-VBUS protection. [ADD, optional but
    cheap]. The optional USBLC6-2SC6 ESD sits on VBUS/CC for surges.
  - **J3 programming header:** the R10/R11 series resistors on U0TXD/U0RXD already block
    backfeed / reversed-plug damage to the C3 pins. Leave the J3 3V3 pin **unwired** (board
    is self-powered while flashing) or run it through a Schottky — never a straight
    connection to a live rail.
  - **Bench-lab safety net:** a TVS (e.g. 5 V on VBUS, 3.3 V on the 3V3 rail) clamps
    over/under-voltages; a reversed voltage forward-biases it → shorts → a series PPTC fuse
    opens. Only worth it if the device will be probed by hand a lot.
- **Battery-status LEDs on the charger** (LED1 red = charging, LED2 green = charged, off the
  TP4056 CHRG/STDBY open-drain outputs via R6/R7 1 kΩ) — wired, zero GPIOs, and the
  "charge status on the LED" spec is satisfied without touching the ESP. A GPIO-driven
  variant (drop Y2 → GPIO0/1) was considered and shelved as redundant.

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

## Component review (14-08-2026)

BOM checked against the target (< $50 excl. IoT plan) — still feasible at ~$20–46. Picks and
why:

- **Antenna for the C3**: the bare chip has no antenna in/on it (dev boards like the AirM2M
  Core / SuperMini carry their own PCB antenna). The tiny needs a **2.4 GHz SMD ceramic chip
  antenna (2450AT43A100-class) + LC matching network**, kept close to the RF pad / feed.
- **Modem stays SIM7670G**: cheaper Cat1 parts (e.g. A7670E / SIM7600-family variants) drop
  GNSS, which fails the hard spec (GNSS + SMS + HTTP/MQTT in one part).
- **Charger: TP4054** (SOT-23-5) instead of TP4056/IP5306 — smallest footprint, right current
  class; TP4056 only if we want the CHRG/STDBY LED pins; IP5306 rejected (5 V boost PMIC,
  wrong tool, bigger).
- **Accelerometer: LIS2DW12** (2×2 mm, ~7 µA). BMA400 as an alternative only if autonomous
  activity detection ever matters (10 µA, slightly pricier).
- **Modem rail**: low-quiescent boost in the ~1 A class (TPS61099-like) plus bulk caps covers
  the 2 A TX bursts; a higher-current part (TPS61023-like) is the headroom option. Both to be
  confirmed against the SIM7670G's burst profile + load switch for sleep.
- **USB-C charge-only**: 4/6-pin receptacle + 5.1 kΩ CC pull-downs; no D+/D- (tiny flashes
  via UART0 pads).

## Open questions

- Footprint ceiling for the cat: is a flat ~30×24 mm collar puck acceptable, or must the
  design fight for something smaller (which re-opens the modem question)?
- Report cadence when moving (sets cell size + charging expectations).
- One LED or none on the tiny version?
- Boot/reset as test pads instead of buttons on the tiny version (no switches on the cat)?
- Waterproofing strategy (sealed USB-C, charging contacts, enclosure) — shapes the final
  form factor.
