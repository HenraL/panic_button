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
-- FILE: bom.md
-- CREATION DATE: 14-08-2026
-- LAST Modified: 2:58:21 14-08-2026
-- DESCRIPTION:
-- A small panic beacon: reports its position to a server, falls back to SMS when unreachable, and advertises BLE (Find My-style) so phones can interact with it without an app.
-- /STOP
-- COPYRIGHT: (c) Asperguide
-- PURPOSE: This is the file containing the list of the components used in the schematic.
-- // AR
-- +==== END AsperHeader =================+
-->
# BOM — panic_button_tiny (KiCad v2, "tiny" variant)

Live bill of materials as of 14-08-2026 (KiCad 10). Prices are rough single-unit estimates
(LCSC / Aliexpress / JLCPCB, USD) — verify before ordering. Cost target: **< $50 / unit,
excluding the IoT/SIM plan** (see `design.md` for all decisions and notes).

## Estimated BOM

| Qty | Ref | Part | Est. price | Notes |
| ----- | ----- | ------ | -----------: | ------- |
| 1 | U5 | ESP32-C3FH4 (QFN-32 5x5, 4MB flash) | $1.50–2.50 | MCU + BLE 5.0 advertising; boot = GPIO9, reset = EN — placed on brains |
| 1 | – | 2.4 GHz ceramic chip antenna (2450AT43A100-class) + LC match | $0.30–0.80 | C3 has no antenna in/on the chip — it needs its own |
| 1 | U1 | SIM7670G LTE Cat1 module | $10–20 | 4G, GNSS, SMS + HTTP/HTTPS + MQTT via AT; IoT M2M SIM — placed on network_and_gps |
| 1 | U3 | TP4056-42-ESOP8 (charger) | $0.15–0.40 | placed as U6 on the Li-ion subsheet; CHRG/STDBY open-drain outputs drive the status LEDs (LED1/LED2) |
| 1 | U7 | DW01A (protection IC) | $0.05–0.15 | **always populated** — enabled/disabled by the toggle jumper on its GND pin (open = battery has its own BMS, closed = bare cell) |
| 1 | U8 | 8205A dual N-MOSFET (SOT-23-6) | $0.07–0.15 | **always populated** — the only cell − → GND path; gates held on by R12/R13 pull-ups when the DW01A is unpowered |
| 1 | Q1 | Si2305CDS P-MOSFET (SOT-23) | $0.05–0.10 | reverse-polarity protection, zero drop — placed, to wire (SW4 → Q1 → +BATT) |
| – | Q2 | Si2301CDS P-MOSFET (SOT-23) | – | **no role — remove** (Q1 covers reverse protection) |
| 1 | SW4 | SPDT slide switch (SMD 6.7×2.6 mm) | $0.20–0.40 | hard on/off in the BT1 (+) path — placed |
| 1 | BT1 | Li-ion pouch cell, 3.7 V (603050-class, 1000 mAh) | $2–5 | size driven by 4G TX bursts + sleep budget |
| 2 | LED1/LED2 | 1.8 mm SMD LED (red + green) | $0.10–0.30 | charging / charged from TP4056 CHRG/STDBY — wired |
| 1 | R8 | 1.2 kΩ 0603 (PROG) | – | TP4056 charge current: 1.2 kΩ = 1 A; 2.4 kΩ = 500 mA if preferred |
| 2 | R6/R7 | 1 kΩ 0603 | – | LED1/LED2 series resistors — wired |
| 1 | – | 2-pad testpoint switch (2.54 mm THT, 0.8 mm drill) | $0.05–0.10 | U7 GND ↔ cell − — the DW01A-enable toggle (µA); ships OPEN (with-BMS default) |
| 2 | R12/R13 | 470 kΩ 0402 | – | OD/OC pull-ups to +BATT — keep the 8205A gates on when the DW01A is unpowered |
| 1 | J2 | USB-C receptacle (charge-only 4/6-pin) | $0.20–0.60 | charge in + 5.1 kΩ CC pull-downs; no D+/D- (flashing via UART0 pads) — placed on power_input |
| 1 | U2 | LIS2DW12 (2x2 mm) | $0.50–1.50 | wake-on-motion interrupt → "if movement" trigger (placed as U2 on accelerometer sheet; LIS2DE12 vs LIS2DW12 still OPEN); BMA400 alt (activity tracking, ~10 µA) |
| 1 | – | LTE antenna (flex PCB, IPEX) | $0.50–2 | |
| 1 | – | GNSS antenna (passive ceramic patch / active) | $1–4 | feeds SIM7670G GNSS input |
| 1 | U3 | 3.3 V LDO ME6211 + decoupling | $0.10–0.30 | MCU rail — placed on power_input |
| 1 | U4 | Modem supply: boost TPS61022 (2.5 A) + L2 1 µH + FB divider + bulk caps | $1–3 | SIM7670G needs 3.4–4.3 V (typ. 4 V) with ~2 A TX peaks; ⚠ currently miswired on the sheet (VIN on 3.3V, VOUT labeled 5V) — to fix |
| – | – | LEDs (status: charge / fix / alert) + resistors | $0.30–1 | |
| – | – | Tact switches (boot / reset), headers, passives | $0.50–1.50 | |
| 1 | – | PCB (JLCPCB, 5 pcs) | $2–5 | |
| – | **Total** | | **~$20–46** | inside the < $50 target |

## Tracking

Keep this file in sync with the schematic (`panic_button_tiny.kicad_sch`) — as of
19-08-2026 (03:21 save): U1 (SIM7670G), U2 (LIS2DW12), U3 (ME6211), U4 (TPS61022), U5 (ESP32-C3),
U6 (TP4056), U7 (DW01A), U8 (8205A), Q1 (Si2305CDS), SW4, BT1, J1, J2, J3, LED1/LED2,
R6–R9 placed (Q2 removed). U7/U8 protection pair rewired (03:21); the cell-side net, the
SW4→Q1→+BATT chain, the toggle jumper and R12/R13 remain to wire/add.
