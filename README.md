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
-- FILE: README.md
-- CREATION DATE: 14-08-2026
-- LAST Modified: 2:57:22 14-08-2026
-- DESCRIPTION:
-- A small panic beacon: reports its position to a server, falls back to SMS when unreachable, and advertises BLE (Find My-style) so phones can interact with it without an app.
-- /STOP
-- COPYRIGHT: (c) Asperguide
-- PURPOSE: This is the file in charge of introducing the repository.
-- // AR
-- +==== END AsperHeader =================+
-->
# Panic Button

A small **panic beacon** (target: no bigger than a finger; a palm if that size can't be
reached) that:

1. Updates its position towards a server.
2. If that is not available, sends an SMS (or similar).
3. Regardless of the above, advertises a BLE beacon (Apple Find My-style, Android hub,
   Samsung if they stray) so nearby phones can interact with it without any app.

Apple UWB is out of reach (proprietary Apple U1/U2 chip + MFi program) — phone interaction is
BLE only. Cost target: **< $50 / unit** (excluding the IoT/SIM plan).

## Repository layout

- `AGENTS.md` — rules and conventions for AI agents and contributors; read it first.
- `KiCad/v1/panic_button` — first board attempt (archive).
- `KiCad/v2/dev/panic_button_dev` — dev variant of the v2 circuit.
- `KiCad/v2/tiny/panic_button_tiny` — **active design** (KiCad 10): the tiny variant,
  as small as possible.
- `KiCad/v2/tiny/bom.md` — live bill of materials (strictly the BOM).
- `KiCad/v2/tiny/design.md` — design decisions, notes and open questions.
- `platformio/` — firmware (PlatformIO + Arduino on ESP32-C3). Comes **after** the circuit
  planning, not in parallel.
- `bonus/` — tools / environments / scratch (`bonus/tmp/`).

## Hardware status

- **Tiny** (active): ESP32-C3 (BLE) + SIM7670G LTE Cat1 (GNSS, SMS, HTTP/MQTT), Li-ion +
  USB-C charging, wake-on-motion accelerometer. Schematic in progress in KiCad 10.
- **Dev** = the tiny circuit + test infrastructure (test points, button array, richer LEDs,
  AirM2M Core ESP32-C3 module as the easier-to-mount MCU), functionally identical.

See `bom.md` for parts and prices, `design.md` for decisions.

## License

MIT — see `LICENSE`.
