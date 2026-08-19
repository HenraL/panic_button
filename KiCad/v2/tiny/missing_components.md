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
-- *@:         @@%       *@@+        :@*
--  @#           @%@*+@@%@#          #@
--  #@%                           . #@*
--    @%                           %@
--     @%@                       @@%
--      -@@%                   #@@=
--         *@@@             @@@#
--             @%@@@@@@@@@@@+
-- /STOP
-- PROJECT: AsperHeader
-- FILE: missing_components.md
-- CREATION DATE: 16-08-2026
-- LAST Modified: 2:50:0 19-08-2026
-- DESCRIPTION:
-- A small panic beacon: reports its position to a server, falls back to SMS when unreachable, and advertises BLE (Find My-style) so phones can interact with it without an app.
-- /STOP
-- COPYRIGHT: (c) Asperguide
-- PURPOSE: This file lists every component the schematic still lacks, the symbol/footprint to use, and where each one links (pin numbers / nets).
-- // AR
-- +==== END AsperHeader =================+
-->
# Missing & present components — panic_button_tiny (as of 19-08-2026)

Complete wiring ledger, pin by pin. Verified against the saved schematic files
(embedded symbols, Y-flip applied). "PROPOSED" = sensible default, owner can
change; "OPEN" = decision pending.

## How to read this file

- `A (pin N) -> B (pin M)` — the wire to draw (arrow = direction of the signal, not of the copper).
- `[ADD]` — component not on the sheets yet; `[PLACED]` — on the sheets but not wired.
- `✓` — already wired and verified by scan. Everything else still needs a wire.
- `REF (pin N)` uses the pin numbers printed on the placed symbol.
- Two-pin passives (R/C/L) have interchangeable pins 1/2.
- Power symbols (GND / 3.3V / 5V) connect across sheets **by name** — placing a
  `3.3V` symbol on another sheet ties it to the same net.

## Nets (power domains)

| Net | Meaning | Source (component-first) |
| --- | ------- | ------ |
| `5V` | USB-C input rail | J2 VBUS |
| `3.3V` | logic rail | U3 V_OUT (ME6211) |
| `BT1 (+)` | battery positive | U6 pin 5 BAT (TP4056), U4 pin 7 VIN, U7 pin 5 VCC (DW01A) — cell +, a *net name* not a connector |
| `BT1 (−)` | battery negative (raw) | U7 pin 6 GND (DW01A) |
| `P_NEG` | protected negative | U8 D2 / jumper pad 1 |
| `U1 VBAT pins 55–57` (net `U1 VBAT pins 55–57`) | ~4.0 V modem rail | U4 V_OUT (TPS61022) |
| `GND` | ground | everywhere |

## Present on the sheets

| Sheet | Ref | Part | Wiring |
| ----- | --- | ---- | ------ |
| brains | U5 | ESP32-C3 (QFN-32) | done ✓ |
| brains | Y1 | C3E-40.000-12-1010-X (40 MHz) | done ✓ |
| brains | C6, C7 | 22 pF crystal load caps (Y1) | done ✓ |
| brains | C8, C9 | 22 pF crystal load caps (Y2) | done ✓ |
| brains | C10–C13 | 100 nF decoupling caps | done ✓ (pin 1 → GND, pin 2 → 3.3V) |
| brains | R5 | 10 kΩ CHIP_EN pull-up (= R_EN) | done ✓ (3.3V @ (180.34, 52.07) → pin 1, pin 2 → CHIP_EN row) |
| brains | R3 | 10 kΩ GPIO9 pull-up (= R_BOOT) | done ✓ (GPIO9 → 3.3V) |
| brains | Y2 | 32.768 kHz crystal (CTS3-32.768) | done ✓ (full network wired to U5 pins 4/5) |
| brains | SW1 | tact switch — BOOT (GPIO9) | done ✓ (pin 2 → GPIO9, pin 1 → GND) |
| brains | SW2 | tact switch — RST (CHIP_EN) | done ✓ (pin 1 → CHIP_EN row, pin 2 → GND) |
| brains | SW3 | tact switch — status button (MTMS) | done ✓ (pin 1 → U5 pin 9 MTMS, pin 2 → GND, see status section) |
| brains | R4 | 4.7 kΩ SDA pull-up | **done ✓ (latest save)** — `3.3V (#PWR055) @ (71.12, 50.8)` → `R4 pin 2 @ (73.66, 50.8)` → `R4 @ (77.47, 50.8) rot 90` → `pin 1 @ (81.28, 50.8)` → `I2C (SDA)` label + GPIO8 (see brains section) |
| brains | J3 | 1×6 UART programming header | done ✓ — all 6 pins wired (3V3, GND, U0TXD, U0RXD, CHIP_EN, GPIO9) |
| brains | TP1–TP6 | test points (3.3V, U0TXD, CHIP_EN, GND, U0RXD, GPIO9) | done ✓ — each wired to its J3 row (see brains section) |
| visual | D1 | WS2812B-2020 status LED (was "D4" in this doc) | done ✓ (23:52 save: 8 wires, D1 @ (43.18, 27.94), C14 100 nF across VDD/VSS, `+BATT` power symbol on VDD (#PWR071), `LED_DIN` wired) — +BATT **sourced ✓** on li-ion (U6 pin 5 BAT, 00:30 save) |
| power_input | J2 | USB-C charge-only | done ✓ |
| power_input | U3 | ME6211C33M5 (3.3 V LDO) | done ✓ |
| power_input | R1, R2 | 5.1 kΩ CC pull-downs | done ✓ (CC2→R2→GND wired; CC1→R1→GND pin-coincident, see note) |
| li-ion_charging_handling | U6 | TP4056-42-ESOP8 charger | to wire |
| li-ion_charging_handling | U7 | DW01A protection | to wire |
| li-ion_charging_handling | BT1 | `Device:Battery_Cell` (1000 mAh) | to wire |
| modem_rail | U4 | TPS61022 boost (modem rail) | to wire — ⚠ VIN on 3.3V net + VOUT labeled 5V (both wrong, see section) |
| network_and_gps | U1 | SIM7670G-LNGV modem | to wire (GND stubs all terminated ✓) |
| network_and_gps | J1 | JAE SF72S006 SIM holder | to wire (GND symbol placed ✓) |
| accelerometer | U2 | LIS2DE12 (design says LIS2DW12 — OPEN) | to wire (Vdd = 3.3V touch ✓, GND touch ✓) |
| sound | LS1 | `PCM_SparkFun-Electromechanical:PKLCS1212E4001-R1` (passive piezo) | wired to `Buzzer+`/`Buzzer-` labels ✓ (23:02) — brains must add matching labels on GPIO3 (conflict with PWRKEY pulse, see sound section) |
| radio_layout | E1 | 2450AT18A0100001E chip antenna | to wire |
| radio_layout | L1, C1 | 4.7 nH + 1.2 pF BLE match | to wire |

## Status ledger (schematic state = 21:00 save, 18-08-2026 — nothing changed since)

One-glance DONE / NOT-DONE per sheet. The U5 pin-by-pin table below is the authoritative
state of the ESP32-C3; "stub done" = the wire + hierarchical label exist on brains,
cross-sheet connection still requires the labels to match.

### brains — U5 (ESP32-C3) pin-by-pin

| U5 pin | Name | State |
| ------ | ---- | ----- |
| 2/3/11/17/18/31 | VDD3P3/RTC/CPU/SPI/VDDA | **DONE ✓** 3.3V bus (y = 40.64) |
| 33 | GND | **DONE ✓** |
| 29/30 | XTAL_P/N (40 MHz) | **DONE ✓** Y1 + C6/C7 |
| 4/5 | XTAL_32K_P/N (32.768 kHz) | **DONE ✓** Y2 + C8/C9 |
| 7 | CHIP_EN | **DONE ✓** R5 pull-up + SW2 + J3 pin 5 + TP3 |
| 9 | MTMS | **DONE ✓** SW3 status button |
| 10 | MTDI | **DONE ✓** Motion_wake label → accelerometer (matches) |
| 15 | GPIO9 | **DONE ✓** R3 pull-up + SW1 + J3 pin 6 + TP6 — **I2C (SCL) FIXED ✓ (latest save)**: label now @ (44.45, 57.15) → stub (48.26, 57.15)→(48.26, 54.61) → GPIO9 row (44.45→86.36, 54.61) — no longer on the 3.3V side of R3 |
| 14 | GPIO8 | **DONE ✓ (latest save)** — full pull-up chain: `3.3V #PWR055 @ (71.12, 50.8)` — wire — `R4 pin 2 @ (73.66, 50.8)` — `R4 @ (77.47, 50.8) rot 90` — `pin 1 @ (81.28, 50.8)` — wire down — row (71.12, 52.07)↔(81.28, 52.07) carrying `I2C (SDA)` label — wire — GPIO8 @ (86.36, 52.07) |
| 6 | GPIO2 | **DONE ✓ (22:30 save)** — label moved to (86.36, 35.56), case fixed (`battery_voltage_sense`), wired up from the pin |
| 8 | GPIO3 | **stub done ✓ (latest save)** — label @ (85.09, 38.1), wired to GPIO3, **name matches the modem sheet** ("power-on_pulse" both sides ✓) — ⚠ **conflict still OPEN**: sound sheet wired LS1 (piezo) to `Buzzer+`/`Buzzer-` labels, but brains has no such labels (see sound section) |
| 27/28 | U0RXD/U0TXD | **DONE ✓** J3 pins 3/4 + TP2/TP5 — **labels now MATCH the modem sheet** ("UART (TXD)"/"UART (RXD)" on both sides ✓) — ⚠ **R_UART_A/B (1 kΩ series) not placed yet** (R6/R7 are taken by the li-ion LED pair — next free refs, see ASCII §7) |
| 16 | GPIO10 | **DONE ✓ (23:52 save)** — `LED_DIN` label @ (78.74, 57.15) wired to GPIO10; **name now MATCHES the visual sheet** (`LED_DIN` both sides) — the LED data net is closed |
| 25 | GPIO18 | **connected ✓ (label anchor)** — modem_status label sits exactly ON the pin tip @ (86.36, 59.69) → net connects (also: STATUS is 1.8 V logic, needs level-shift) |
| 26 | GPIO19 | **connected ✓ (label anchor)** — modem_rail_switch label sits exactly ON the pin tip @ (86.36, 62.23) → net connects |
| 1 | LNA_IN | untouched (antenna lives on radio_layout) |

### All sheets, done vs not

| Sheet | Done | Not done |
| ----- | ---- | -------- |
| brains | everything above + J3 (6/6) + TP1–TP6 + R3/R5 + SW1/SW2/SW3 | the ⚠ rows above (labels/wiring hygiene), DIN label |
| power_input | **complete ✓** (30 wires, verified twice) | cosmetic: draw CC1→R1 and R2 pin 2→GND wires |
| visual | D1 WS2812B wired ✓ (8 wires) + C14 decap ✓ + `+BATT` symbol ✓ | `+BATT` now sourced on li-ion ✓ (00:30 save) |
| network_and_gps | 39 GND stubs + GND symbol + 5 labels (UART TXD/RXD, battery_voltage_sense, power_on_pulse, modem_status) | all signal wiring U1/J1; STATUS level-shift |
| accelerometer | U2 placed, Vdd/GND touches, 3 labels (I2C (SCL)/(SDA), Motion_wake) | all wiring |
| modem_rail | — | **everything** (VIN wrong net, VOUT labeled 5V, L2/FB/caps/EN/MODE) |
| li-ion_charging_handling | U6/U7/BT1 placed | **everything** (0 wires) |
| sound | BZ1 placed, 2 labels | **everything** |
| radio_layout | E1/L1/C1 placed | **everything** (0 wires) |

### Open decisions (blocking wiring)

1. GPIO3: buzzer (BZ1) vs power-on pulse (PWRKEY)
2. D1 (WS2812) VDD rail: BT1 (+) pin (recommended) vs 3.3V vs U1 VBAT (4.0 V)
3. Charge-status on LED: A = drop Y2 → GPIO0/1 = CHRG/STDBY (recommended), B = keep D1/D2 LED pair, C = sacrifice buzzer
4. U2: LIS2DE12 placed vs design LIS2DW12

## ASCII wiring references (for the wrong / unclear connections)

### 1. R4 — I2C SDA pull-up (brains sheet, DONE ✓, latest save)

```txt
NOW:  full pull-up chain verified — 3.3V symbol (#PWR055) @ (71.12, 50.8), R4 @ (77.47, 50.8)
      rot 90 (horizontal), pin 2 @ (73.66, 50.8) ← wire ← 3.3V; pin 1 @ (81.28, 50.8)
      → wire down → row y = 52.07 → I2C (SDA) label @ (71.12, 52.07) → GPIO8 @ (86.36, 52.07)

    #PWR055 3.3V ── R4 pin 2 ──[R4]── R4 pin 1 ──┬── I2C (SDA) label ──→ U2 pin 4
                                                  └── GPIO8 (U5 pin 14)
```

### 2. I2C (SCL) label — FIXED ✓ (brains sheet, latest save)

```txt
NOW (fixed):  the label moved to the GPIO9 side of R3 — label @ (44.45, 57.15),
              vertical stub (48.26, 57.15)→(48.26, 54.61), then the GPIO9 row
              (44.45→86.36, 54.61) → GPIO9 (U5 pin 15). R3: 3.3V #PWR052 @ (34.29, 54.61)
              → pin 1 @ (36.83, 54.61), pin 2 @ (44.45, 54.61) = row start.

    3.3V ── R3 10k ──┬── GPIO9
                     └── I2C (SCL) label ──→ U2 pin 1   (label on the MCU node) ✓
```

### 3. Cross-sheet net names — ALL FIXED ✓ (all sheets, latest save)

```txt
KiCad labels are case- AND punctuation-sensitive. Pick ONE name, use it on both sheets:

    brains                         other sheet          verdict
    ─────────────────────────────  ──────────────────   ────────────────
    "UART (TXD)"                   "UART (TXD)"         ✓ MATCH (modem sheet renamed)
    "UART (RXD)"                   "UART (RXD)"         ✓ MATCH
    "battery_voltage_sense"        "battery_voltage_sense"  ✓ FIXED (22:30)
    "power-on_pulse"               "power-on_pulse"     ✓ MATCH (modem sheet renamed to hyphen)
    "Motion_wake"                  "Motion_wake"        ✓ OK
    "modem_status"                 "modem_status"       ✓ OK — and now anchored ON GPIO18's tip
    "modem_rail_switch"            "modem_rail_switch"  ✓ OK — and now anchored ON GPIO19's tip
```

### 4. LED data line — DONE ✓ (23:52 save, brains ↔ visual sheets)

```txt
[visual ✓]  "LED_DIN" label @ (34.29, 27.94) — wire — D1 pin 3 (DIN) @ (35.56, 27.94)
[brains ✓]  "LED_DIN" label @ (78.74, 57.15) — wire — U5 pin 16 (GPIO10) @ (86.36, 57.15)

    brains:  U5 pin 16 GPIO10 ── "LED_DIN" ──⇄── "LED_DIN" ── D1 DIN   (visual) ✓ CLOSED
```

### 5. D1 VDD — power rail (visual sheet; WS2812B needs 3.7–5.3 V)

```txt
USER DECISION (23:52 save): D1 VDD is on the "+BATT" power symbol (now #PWR071 after the 01:56 annotation) —
the rail must be SOURCED on the li-ion sheet: place a "+BATT" power symbol at
U6 pin 5 BAT (downstream of SLIDE_SW). See visual section for the honest notes
(U6 BAT = same node as the cell +; Q2 P-FET turns +BATT into the protected rail;
3.3V from U3 = the regulated alternative, dimmer).

    li-ion:  BT1 (+) ── Q2 [optional] ── U6 pin 5 BAT ── [+BATT symbol]
    visual:  [+BATT symbol] ── C14 100nF ── D1 VDD
```

### 6. modem_rail sheet — the two wrong nets (biggest electrical risk)

```txt
NOW (wrong):  U4 VIN on the 3.3V net;  U4 VOUT labeled 5V

    [3.3V] ── U4 VIN      (500 mA LDO can't feed 2 A TX bursts)
    U4 VOUT ── [5V]       (SIM7670G VBAT max = 4.3 V → would kill the modem)

FIX:

    BT1 (+) pin ──┬────────────── U4 pin 7 VIN        (battery rail, +22µF)
               └── L2 1µH ──── U4 pin 2 SW
    U4 pin 3 VOUT ──┬── U1 VBAT (4.0 V) (4.0 V net) ──→ U1 VBAT pins 55–57   (+22µF)
                    └── R_FB_HI 700k ──┬── U4 pin 4 FB
                                       └── R_FB_LO 100k ── GND
    U4 pin 5 EN ──── (from brains: "modem_rail_switch" label ⇄ GPIO19)
    U4 pin 6 MODE ── BT1 (+) pin      (PFM, light-load idle)
    U4 pin 1 GND ─── GND
```

### 7. R_UART_A/B — programmer backfeed protection (brains sheet; 1 kΩ series, NOT placed yet)

```txt
The two 1 kΩ go IN SERIES between U5's UART pins and the junction where the J3/TP and
label branches split — so everything downstream (J3, TPs, modem label) sits on the
protected side. Will become R6/R7 on the next annotation.

    U0TXD row (y = 67.31):   U5 pin 28 ── R6 1k ──┬── J3 pin 3 ─ TP2        (long run down)
                                                  └── "AT UART (TXD)" label
    U0RXD row (y = 69.85):   U5 pin 27 ── R7 1k ──┬── J3 pin 4 ─ TP5        (long run down)
                                                  └── "AT UART (RXD)" label

    (Insert in the short gaps: R6 between (86.36, 67.31) and (82.55, 67.31),
     R7 between (86.36, 69.85) and (81.28, 69.85).)
```

## Wiring — already done (verified by scan, 19-08-2026)

**brains (60 wires, re-laid out 19-08-2026 — U5 @ (109.22, 67.31), Y1 @ (22.86, 73.66)):**

- `U5 (pin 30 XTAL_P) -> Y1 (pin 1)` via C6 ✓ (bus y = 82.55)
- `U5 (pin 29 XTAL_N) -> Y1 (pin 3)` via C7 ✓ (routed along y = 80.01)
- `C6 (pin 1) <-> C7 (pin 2)` — coincident midpoint @ (30.48, 95.25) ✓
- `Midpoint -> Y1 (pin 2 case GND)` -> `GND` ✓ (case pin + GND symbol @ (30.48, 97.79))
- `Y1 (pin 4 case GND)` — floats, OK
- `U5 (pin 33 GND) -> GND` ✓
- **3.3 V rail — all five power pins on the y = 40.64 bus:** `3.3V -> U5 (pin 2 VDD3P3)`, `3.3V -> U5 (pin 11 VDD3P3_RTC)`, `3.3V -> U5 (pin 17 VDD3P3_CPU)`, `3.3V -> U5 (pin 18 VDD_SPI)`, `3.3V -> U5 (pin 31 VDDA)` ✓
- **Decoupling fixed — caps now parallel, not series:** `C10/C11/C12/C13 (pin 1) -> GND` (chain @ y = 22.86/35.56 from the GND symbol @ (138.43, 35.56)), `C10/C11/C12/C13 (pin 2) -> 3.3V` (the 40.64 bus) ✓
- **32 kHz RTC network — new, fully wired ✓:** `Y2 (pin 1) -> C8 -> U5 (pin 5 XTAL_32K_N)`, `Y2 (pin 2) -> C9 -> U5 (pin 4 XTAL_32K_P)` (buses y = 86.36 / y = 74.93), `C8/C9 (far ends) -> GND` @ (49.53, 78.74)
- **R_EN (R5) @ (172.72, 52.07):** `3.3V @ (180.34, 52.07) -> R5 (pin 1)` ✓ — `R5 (pin 2) -> U5 (pin 7 CHIP_EN)` ✓ (real wire, row @ (153.67, 52.07))
- **R_BOOT (R3) @ (40.64, 54.61):** `3.3V @ (34.29, 54.61) -> R3 (pin 1)` ✓ — `R3 (pin 2) -> U5 (pin 15 GPIO9)` ✓ (row @ (59.69, 54.61))
- **SW_RST (SW2) @ (158.75, 46.99) — done ✓:** `SW2 (pin 1) -> CHIP_EN` (row @ (153.67, 52.07)), `SW2 (pin 2) -> GND` (touch @ (163.83, 46.99))
- **SW_BOOT (SW1) @ (54.61, 52.07) — done ✓:** `SW1 (pin 2) -> GPIO9` (junction @ (59.69, 54.61)), `SW1 (pin 1) -> GND` (touch @ (49.53, 52.07))
- **Status button (SW3) @ (191.77, 57.15) — placed & wired ✓:** `SW3 (pin 1) -> U5 (pin 9 MTMS)` (row @ y = 57.15 from pin 9 @ (132.08, 57.15)), `SW3 (pin 2) -> GND` (symbol @ (199.39, 57.15)) — was "SW4" in this doc, re-annotated to SW3

> **✓ 21:00 version — 92 wires: TPs added, J3 complete, R4/R5 re-annotated**
>
> **Annotation (21:00 save):** the 4.7 kΩ SDA resistor and the 10 kΩ R_EN **swapped refs** —
> **R4 = 4.7 kΩ (SDA series, @ (78.74, 52.07))**, **R5 = 10 kΩ (R_EN, @ (172.72, 52.07))**.
> J3 moved to (241.3, 58.42), then again to (245.11, 58.42) (22:30 save). New **TP1–TP6** (TestPoint, brains) wired to the J3 rows:
> TP1 = 3.3V @ (217.17, 52.07), TP2 = U0TXD @ (217.17, 57.15), TP3 = CHIP_EN @ (217.17, 62.23),
> TP4 = GND @ (231.14, 54.61), TP5 = U0RXD @ (231.14, 59.69), TP6 = GPIO9 @ (231.14, 64.77) —
> all ✓ WIRED.
>
> **J3 (1×6 UART header) @ (245.11, 58.42) — all 6 pins wired ✓:**
>
> - `J3 (pin 1 3V3)` ✓ — row @ y = 52.07 (via TP1, 3.3V symbol @ (214.63, 52.07)) — **was the last free pin, now done**
> - `J3 (pin 2 GND)` ✓ (row @ y = 54.61 via TP4; GND symbol @ (209.55, 54.61))
> - `J3 (pin 3 U0TXD) -> U5 (pin 28 U0TXD)` ✓ (routed @ y = 102.87; TP2 on the row)
> - `J3 (pin 4 U0RXD) -> U5 (pin 27 U0RXD)` ✓ (routed @ y = 104.14; TP5 on the row)
> - `J3 (pin 5 CHIP_EN) -> CHIP_EN row` ✓ (column @ (153.67, 62.23) up to y = 52.07; TP3 on the row)
> - `J3 (pin 6 GPIO9) -> GPIO9 row` ✓ (routed @ y = 105.41; TP6 on the row)
> **R4 (4.7 kΩ, 0603) @ (77.47, 50.8) rot 90 — DONE ✓ (latest save):**
> - Full pull-up: `3.3V (#PWR055) @ (71.12, 50.8)` — wire — `R4 pin 2 @ (73.66, 50.8)` — wire
>   `(81.28, 50.8) → (81.28, 52.07)` — `R4 pin 1 @ (81.28, 50.8)` — row (71.12, 52.07)↔(81.28, 52.07)
>   with `I2C (SDA)` label — wire — `GPIO8 @ (86.36, 52.07)` ✓
> **Peripheral stubs + hierarchical labels (brains side):**
> - `U5 (pin 10 MTDI) -> Motion_wake` ✓ — label @ (135.89, 59.69); matches accelerometer ✓
> - `U5 (pin 6 GPIO2) -> Battery_voltage_sense` @ (86.36, 41.91) — ⚠ **case mismatch** with
>   the modem sheet's `battery_voltage_sense` → not connected
> - `U5 (pin 8 GPIO3) -> power-on_pulse` @ (85.09, 44.45) — ⚠ **hyphen vs underscore**
>   (`power_on_pulse` on the modem sheet) → not connected; ⚠ also conflicts with the buzzer
>   decision (GPIO3 = BZ1, freed by the PWRKEY cap) — see sound section
> - `AT UART (TXD/RXD)` labels @ (74.93, 67.31/69.85) on the U0TXD/U0RXD rows — ⚠ **names
>   differ** from the modem sheet's `UART TXD`/`UART RXD` → not connected
> - `modem_status` label @ (86.36, 59.69) — name matches the modem sheet ✓, but **no wire to
>   a U5 pin** (GPIO18 not wired) → dangling
> - `modem_rail_switch` label @ (86.36, 62.23) — matches modem_rail ✓, but **no wire to
>   U5 pin 26 (GPIO19)** → dangling
> - `I2C (SCL)` label @ (29.21, 54.61) — ⚠ placed on the **3.3V side of R3** (old dangling
>   stub) → SCL would be hard-tied to 3.3V, NOT to GPIO9. Move it to the GPIO9 row
>   (e.g. @ (59.69, 54.61) junction or between R3 pin 2 and U5 pin 15)
> - `I2C (SDA)` label @ (71.12, 52.07) — on R4 pin 2 (see R4 above)
>
> **ℹ Note: R3/R5 swapped roles vs. this doc's earlier revision** — R3 is now the GPIO9
> pull-up (R_BOOT), R5 the CHIP_EN pull-up (R_EN); the 4.7 kΩ SDA resistor is R4.
>
> **✓ 18:10 version — switches fixed, verified by scan:**
>
> - **SW1 (BOOT) @ (54.61, 52.07):** `SW1 (pin 2) -> GPIO9` (row junction @ (59.69, 54.61),
>   between R3 pin 2 and U5 pin 15) ✓; `SW1 (pin 1) -> GND` (touch @ (49.53, 52.07)) ✓.
>   Hold SW1 + tap SW2 → download mode. No rail short: the 10 kΩ stays on the 3.3V side.
> - **SW2 (RST) @ (158.75, 48.26):** `SW2 (pin 1) -> CHIP_EN` (row @ (153.67, 52.07), from
>   U5 pin 7 @ (132.08, 52.07)) ✓; `SW2 (pin 2) -> GND` (touch @ (163.83, 48.26)) ✓.
>   Press = CHIP_EN low directly (R5 pulls it back up). Correct reset switch.
> - **R_BOOT (R3) @ (40.64, 54.61):** `3.3V @ (34.29, 54.61) -> R3 (pin 1)` ✓;
>   `R3 (pin 2) -> GPIO9` ✓.
> - **R_EN (R5) @ (172.72, 52.07):** `3.3V @ (180.34, 52.07) -> R5 (pin 1)` ✓;
>   `R5 (pin 2) -> CHIP_EN` ✓ (real wire).
>
> Cosmetic leftovers: dangling wire ends @ (29.21, 54.61) (left of the R3 pull-up symbol)
> and @ (43.18, 54.61) may still exist — harmless, can be trimmed. Switch↔GND links are
> pin-touches — check the junction dots render.
>
> **ℹ Coincident pins = connected in KiCad** (a junction dot renders there), but they are
> fragile: R3/R5, the switches and the 3.3V/GND symbols rely on pins touching without
> wires. If a pin is nudged, the connection silently disappears — check the junction dots
> render.

**power_input (30 wires, 19:37 save):**

- `J2 (VBUS pin 1) -> 5V` and `J2 (VBUS pin 2) -> 5V` ✓
- `5V -> U3 (pin 1 V_IN)` ✓
- `U3 (pin 5 V_OUT) -> 3.3V` ✓
- `J2 (GND pin 1) -> GND`, `J2 (GND pin 2) -> GND`, `J2 (SHIELD) -> GND` ✓
- `U3 (pin 2 V_SS) -> GND` ✓
- `J2 (CC1) -> R1 (pin 1)` ✓ (pins touch, no wire needed) — `R1 (pin 2) -> GND` ✓
- `J2 (CC2) -> R2 (pin 1)` ✓ — `R2 (pin 2) -> GND` ✓

## Wiring — TO DO, sheet by sheet

### brains

| Wire | Notes |
| ----- | ---- |
| `U5 (pin 27 U0RXD) -> U1 (pin 9 TXD)` | modem AT UART — stub + label done (⚠ label "AT UART (RXD)" ≠ modem sheet "UART RXD") |
| `U5 (pin 28 U0TXD) -> U1 (pin 10 RXD)` | modem AT UART — stub + label done ✓ (labels now "UART (TXD)"/"UART (RXD)" on both sheets) — ⚠ R_UART_A/B not placed yet (see ASCII §7) |
| `U5 (pin 14 GPIO8) -> U2 (pin 4 SDA/SDI)` | I2C SDA — **DONE ✓ (pull-up via R4 to 3.3V)**; label "I2C (SDA)" ✓ |
| `U5 (pin 15 GPIO9) -> U2 (pin 1 SCL/SPC)` | I2C SCL — **DONE ✓ (label on the GPIO9 row since 23:02)** |
| `U5 (pin 10 MTDI = GPIO5) -> U2 (pin 12 INT1)` | motion wake — stub + label "Motion_wake" ✓ matches accelerometer |
| `U5 (pin 6 GPIO2) -> U1 (pin 51 VBAT_ADC)` | battery voltage sense — stub + label ✓ (case fixed) |
| `U5 (pin 9 MTMS = GPIO4) -> SW3 (pin 1)` | **DONE ✓ (19:37 save)** status button; `SW3 (pin 2) -> GND` — short = battery color on D1, long = BLE pairing (LED, not buzzer) |
| `U5 (pin 8 GPIO3) -> U1 (pin 1 PWRKEY)` | power-on pulse — stub + label ✓ (names match since 23:02) — ⚠ **conflicts with the buzzer decision** (GPIO3 = BZ1/LS1), see sound section |
| `U5 (pin 25 GPIO18) -> U1 (pin 66 STATUS)` | modem status — **connected ✓ (label anchored ON the pin tip @ (86.36, 59.69))**; ⚠ STATUS is 1.8 V logic, needs level-shift |
| `U5 (pin 26 GPIO19) -> U4 (pin 5 EN)` | modem rail switch — **connected ✓ (label anchored ON the pin tip @ (86.36, 62.23))** |
| `U5 (pin 16 GPIO10) -> D1 (pin 3 DIN)` | status LED data — **DONE ✓ (23:52 save)** — `LED_DIN` on both sheets, both wired |

#### Programming interface (BOOT/RST switches + UART pads)

Why R_EN and R_BOOT exist (the two 10 kΩ you placed):

- **R_EN (R5)** — `CHIP_EN` is the chip's enable; the C3 stays on while it is high. The 10 kΩ to 3.3 V guarantees it is pulled up at power-on. The programmer (or SW_RST) pulls it low to reset.
- **R_BOOT (R3)** — `GPIO9` is a **strapping pin**: if it is LOW at power-up, the C3 enters **download mode** (UART bootloader) instead of running the firmware. The 10 kΩ to 3.3 V keeps it high = normal boot. To flash: hold GPIO9 low (SW_BOOT or the programmer pad) while resetting, then release.

Switches already on the sheets:

| [PLACED] | Part | Wiring | Status |
| -------- | ---- | ------ | ------ |
| SW_RST (SW2) | momentary tact, 4.5×4.5 mm SMD (`Switch:SW_Push`) | `U5 (pin 7 CHIP_EN)` ↔ GND | ✓ done (18:10 version) |
| SW_BOOT (SW1) | momentary tact, same | `U5 (pin 15 GPIO9)` ↔ GND | ✓ done (18:10 version) |
| J3 | **UART programming header — 1×6 pin, 2.54 mm THT** (`Connector_PinHeader_2.54mm:PinHeader_1x06_P2.54mm_Vertical`), or 6 test points / castellated pads | **done ✓ @ (245.11, 58.42) — all 6 pins wired (22:30 save)** — plus TP1–TP6 test points on the same rows | ✓ done (22:30 save) |

| Pad # | Net | Wiring | Status |
| ----- | --- | ------ | ------ |
| 1 | 3V3 | from the programmer (strapping a 2-pin bridge powers the bare board) | ✓ done (21:00) — row @ y = 52.07, via TP1 |
| 2 | GND | `J3 (pin 2) -> GND` (row @ y = 54.61, via TP4; GND symbol @ (209.55, 54.61)) | ✓ done (21:00) |
| 3 | U0TXD | `J3 (pin 3) -> U5 (pin 28 U0TXD)` — C3 TX → programmer RX (routed @ y = 102.87, via TP2) | ✓ done (21:00) |
| 4 | U0RXD | `J3 (pin 4) -> U5 (pin 27 U0RXD)` — C3 RX ← programmer TX (routed @ y = 104.14, via TP5) | ✓ done (21:00) |
| 5 | CHIP_EN | `J3 (pin 5) -> U5 (pin 7 CHIP_EN)` — programmer pulses low→high to reset into bootloader (column @ (153.67, 62.23), via TP3) | ✓ done (21:00) |
| 6 | GPIO9 | `J3 (pin 6) -> U5 (pin 15 GPIO9)` — programmer drives LOW at reset → download mode (routed @ y = 105.41, via TP6) | ✓ done (21:00) |

The THT header doubles as a test jig for the dev variant; on tiny it can be
castellated/solder pads instead if the case forbids the height.

> **⚠ Programmer backfeed / reversed-wiring protection (18-08-2026):**
>
> - **Hazard:** a backwards-plugged or 5 V-logic USB-serial adapter can (a) put 5 V TX onto
>   our U0RXD pad (C3 is NOT 5 V-tolerant), (b) TX↔TX fight both push-pull drivers, (c) feed
>   its VCC (3.3 V, or 5 V on cheap clones!) into the 3.3 V rail.
> - **[ADD] R_UART_A + R_UART_B: 1 kΩ 0402 in series on U0TXD and U0RXD**, near the J3/TP
>   side. 5 V TX → clamp current limited to ~1.5 mA; TX↔TX conflict → ~3.3 mA max; zero
>   signal impact at 115200 baud. The cheapest real protection.
> - **Preferred flashing flow: power the board via USB-C (J2), not J3 pin 1** — then the
>   programmer only carries GND + 4 signals and has no VCC backfeed path at all. J3 pin 1
>   (3V3) stays as a bare-board jig option.
> - (Optional) BAT54 Schottky in series on J3 pin 1 if programmer-powering matters: blocks
>   reverse current, costs 0.3 V (C3 min VDD 3.0 V — tight but usable).
> - A fully-reversed 6-pin plug (VCC onto the GND pin) beats any of this — key the
>   connector / label the header, and never connect the programmer's 5 V VCC pin.

### power_input

| Wire | Notes |
| ---- | ----- |
| `U3 (pin 3 CE) -> 5V` | enable the LDO — tie to VIN (internal pull-up exists, explicit tie is safer) — **DONE ✓ (CE wired to the VIN row @ (130.81, 35.56))** |
| `U3 (pin 4 NC)` | leave unconnected |
| `C3 (1 µF) -> 5V` and `C3 -> GND` | **DONE ✓ (19:37 save)** 1 µF input cap at U3 VIN — ME6211 datasheet minimum, near pin 1; C3 @ (118.11, 45.72) rot 180 |
| `C4 (10 µF) + C5 (100 nF) -> 3.3V` and `-> GND` | **DONE ✓ (19:37 save)** 10 µF + 100 nF at U3 VOUT — LDO stability + rail bulk; C4 @ (151.13, 41.91), C5 @ (151.13, 50.8) |
| `C2 (10 µF) -> 5V` and `C2 -> GND` | **DONE ✓ (19:37 save)** 10 µF near J2 VBUS (cable inductance); C2 @ (21.59, 38.1) |

Review verdict 19-08-2026: power_input is now **complete and electrically correct** —
VBUS×2 -> C2 -> U3 VIN (+ C3 at VIN) ✓, U3 CE -> VIN row ✓, U3 VSS -> GND ✓,
U3 VOUT -> 3.3V + C4 + C5 ✓, J2 GND×2 + SHIELD -> GND row ✓.
⚠ **CC1 -> R1** relies on **coincident pins** @ (49.53, 36.83) — connected, but draw an
explicit wire (same for R2 pin 2 @ (64.77, 39.37) -> GND symbol). CC2 -> R2 is a real wire ✓.

### li-ion_charging_handling

> **⚠ Symbol pinouts on this sheet (read from the embedded blocks, 01:40 save) — several
> differ from the stock-KiCad versions this doc quoted before:**
>
> - **U7 DW01A** (`Battery_Management`): 1 = OD, 2 = CS, 3 = OC, 4 = TD, 5 = VCC, 6 = GND ✓ matches
> - **U8 8205A** (custom SnapMagic-style lib `8205A`) — **symbol RESTORED via fresh SnapEDA
>   download (02:28, user action)** after another model corrupted the pin names, then
>   **FIXED by the user (02:47): pin 5 split from pin 2 and made visible** — pins 2 + 5
>   are now two separate drain pins (2 @ (-1.27, 7.62) → tip (161.29, 36.83), 5 @
>   (2.54, 7.62) → tip (165.10, 36.83)). Symbol is anonymous (no pin names). Physical part
>   (UMW datasheet, 19-08-2026): **1 = S1, 2 = D, 3 = S2, 4 = G2 (charge gate), 5 = D,
>   6 = G1 (discharge gate)**.
> - **Q1/Q2** (`PCM_Transistor_MOSFET_AKL` Si2305CDS / Si2301CDS): **1 = G, 2 = S, 3 = D** (SOT-23)
> - **SW4** (`PCM_SparkFun-Switch` SPDT_Slide): **1 = A, 2 = 2, 3 = C**, plus MT1–MT4 mounting tabs
> - **U6 TP4056-42-ESOP8** (`Battery_Management`): 1 TEMP, 2 PROG, 3 GND, 4 VCC, 5 BAT,
>   6 STDBY, 7 CHRG, 8 CE, 9 EPAD ✓ matches

Parts on the sheet (01:40 save): **BT1** Battery_Cell @ (19.05, 36.83), **SW4** SPDT slide
switch @ (38.10, 31.75) — the hard on/off, **Q1** Si2305CDS @ (57.15, 31.75) rot 90,
**Q2** Si2301CDS @ (170.18, 63.50) — reverse-protection FETs, **U6** TP4056 @ (81.28, 43.18),
**U7** DW01A @ (143.51, 31.75), **U8** 8205A @ (162.56, 44.45) — protection pair,
**LED1** red @ (50.80, 43.18) + **LED2** green @ (50.80, 48.26), **R6**/**R7** 1 kΩ,
**R8** 1.2 kΩ, **R9** 10 kΩ, **TP7**–**TP11** test points. Power symbols (01:56 annotation):
`-BATT #PWR064` @ (19.05, 46.99) — NEW, on the BT1 (−) rail, `GND #PWR065` @ (19.05, 67.31),
`GND #PWR066` @ (45.72, 48.26), `+BATT #PWR067` @ (66.04, 29.21), `3.3V #PWR068`
@ (69.85, 38.10), `5V #PWR069` @ (81.28, 27.94), `GND #PWR070` @ (81.28, 59.69).
(visual: `+BATT #PWR071` @ (19.05, 20.32), `GND #PWR072` @ (19.05, 39.37).)

Charger & LEDs wired ✓ (01:18–01:40): `BT1 (+) -> SW4 (pin 2)`, `BT1 (−) -> GND #PWR065`
(+ long bottom route to U7; the new `-BATT #PWR064` symbol labels the same net), `5V -> U6 (pin 4 VCC)`, `U6 (pin 8 CE) -> 3.3V`,
`U6 (pin 5 BAT) -> +BATT #PWR067` (rail source; TP10 taps it @ (109.22, 29.21)),
`U6 (pin 7 CHRG) -> R6 -> LED1 red -> GND #PWR065`, `U6 (pin 6 STDBY) -> R7 -> LED2 green ->
GND`, `U6 (pin 2 PROG) -> R8 -> GND #PWR069`, `U6 (pin 3 GND + pin 9 EPAD) -> GND`,
`U6 (pin 1 TEMP) -> TP7`. On/off chain (01:40 → 01:56): `SW4 (pin 1 A) -> Q1 (pin 2 S)`,
`Q1 (pin 3 D) -> +BATT #PWR067`, and at 01:56 **`Q1 (pin 1 G) -> BT1 (−)` — the
SW4 → Q1 → +BATT chain is fully closed ✓**.

| Wire | Notes |
| ---- | ----- |
| `BT1 (+) -> SW4 (pin 2)` | ✓ done |
| `SW4 (pin 1 A) -> Q1 (pin 2 S)`, `Q1 (pin 3 D) -> +BATT`, **`Q1 (pin 1 G) -> BT1 (−)/GND`** | ✓ **ALL DONE (01:56 save)** — gate wire @ (55.88, 35.56) → (39.37, 35.56) → (39.37, 40.64) → (19.05, 40.64) → BT1 (−) @ (19.05, 39.37). **The SW4 → Q1 → +BATT chain is now fully closed — the rail is live** |
| `U6 (pin 4 VCC) -> 5V` | ✓ done — 5V is global (USB-C from power_input, direct net: li-ion is a **subsheet of power_input** by design, so the battery/5V nets connect straight across) |
| `U6 (pin 5 BAT) -> +BATT` | ✓ done — via +BATT #PWR067 @ (66.04, 29.21) + TP10 probe |
| `U6 (pin 8 CE) -> 3.3V` | ✓ done — #PWR067 @ (69.85, 38.10) |
| `U6 (pin 3 GND) -> GND`, `U6 (pin 9 EPAD) -> GND` | ✓ both done — #PWR069 @ (81.28, 59.69) |
| `U6 (pin 2 PROG) -> R8 (1.2 kΩ) -> GND` | ✓ done — ⚠ **1.2 kΩ = 1 A charge current**; doc recommended 2.4 kΩ (500 mA, 0.5 C). Fine if the cell accepts 1 A; otherwise swap R8 to 2.4 kΩ |
| `U6 (pin 7 CHRG) -> R6 (1 kΩ) -> LED1 (red) -> GND` | ✓ done — charging indicator (open-drain, active low) |
| `U6 (pin 6 STDBY) -> R7 (1 kΩ) -> LED2 (green) -> GND` | ✓ done — charged indicator |
| `U6 (pin 1 TEMP) -> TP7` | ✓ done — floating test point; NTC only if the cell has a TS pin |
| `U7 (pin 5 VCC) -> TP11` | ⚠ **wired to a test point ONLY — the DW01A is NOT powered.** VCC must also go to `BT1 (+)` (it can share the +BATT net through the same TP row) |
| `U7 (pin 6 GND) -> BT1 (−)` | ✓ done — bottom route @ y = 66.04 |
| `U7 (pin 2 CS) -> U8 (pin 1 S1 + pin 3 S2)` | ✓ done — the mid node (both 8205A sources) |
| `U7 (pin 1 OD) -> U8 (pin 6)` | ✓ **done (02:30 save) — MOVED from pin 2 to pin 6**, the real discharge gate **G1** per the UMW datasheet (the old wire landed on a drain) |
| `U7 (pin 3 OC) -> U8 (pin 4)` | ✓ **correct** — pin 4 is the real charge gate **G2** (datasheet), despite the old symbol's "D" label; wire unchanged |
| `U7 (pin 4 TD)` | leave open ✓ (nothing wired) |
| `U8 (pins 2 + 5) -> P_NEG -> jumper -> GND` | ⚠ missing — the real drains (pins 2 + 5, interchangeable D1/D2) go nowhere yet; one side = cell −, the other = P_NEG → solder jumper (`P_NEG <-> GND`, bridged by default, **not placed on the sheet**). ✓ drains are now SEPARATE pins (user split pin 5, 02:47) — the bare-cell mode is wireable |
| `U8 (pin 6)` | ✓ wired — the OD target (real G1); note the old symbol called it "D" |
| `Q2 (Si2301CDS) @ (170.18, 63.50)` | ⚠ completely unwired — **no role; recommend REMOVE** (Q1 covers reverse protection, U7/U8 cover the cell) |
| `R9 (10 kΩ) + TP8/TP9` | ⚠ floating stub — pin 1 -> TP8 @ (101.60, 43.18), pin 2 touching TP9 @ (102.87, 54.61); no net to anything. Battery-sense divider start, or delete? |
| `SW4 (pin 3 C) + MT1–MT4` | spare throw + mounting tabs — leave unwired ✓ |

> **ℹ Reading the cell nodes (18-08-2026):** `BT1` (the `Device:Battery_Cell` symbol on
> this sheet) **is** the battery — a 3.7 V nominal Li-ion pouch in the real device (3.0–4.2 V;
> cell wires soldered or a keyed 2-pin connector, e.g. Molex PicoBlade — a 2-pin JST-PH is
> NOT reliably keyed and can be plugged backwards). `BT1 (+)` / `BT1 (−)` are the symbol's
> actual pin names — treat them as *net names*, not connectors.
>
> **ℹ Hierarchy (user design):** li-ion_charging_handling is a **subsheet of power_input**
> (sheet "Li-ion charging handling" inside power_input.kicad_sch) — the battery and its
> charger live in the power path by design, so 5V and +BATT connect **directly** between the
> two sheets (global power nets, no labels) for stability.
>
> **BMS toggle (user spec, 19-08-2026) — the protection section is OPTIONAL by population:**
>
> - **Default — cell WITH internal BMS** (most 603050 pouches ship with a protection PCB
>   inside the wrapper): jumper **bridged** (= cell − goes straight to GND), **U7/U8 NOT
>   populated**. Cost 0, and the DW01A/8205A don't interfere.
> - **Bare cell (no BMS)**: open the jumper, populate **U7 (DW01A) + U8 (8205A)** — cell − →
>   U8 drain → the FETs → other U8 drain → P_NEG → board GND; DW01A **OD → U8 pin 6 (G1)** /
>   **OC → U8 pin 4 (G2)** (overcharge / overdischarge / overcurrent protection). ⚠ when
>   U7/U8 are populated, the direct `BT1 (−) -> GND #PWR065` wire must be cut — the cell
>   must reach GND only through the 8205A (the `-BATT #PWR064` label marks the cell-side
>   rail). ✓ the drains (pins 2 + 5) are separate in the symbol since 02:47.
> - The board always carries the footprints (U7, U8, jumper) — population is the toggle.
>
> **Reverse-polarity protection — Q1 (Si2305CDS @ (57.15, 31.75)): FULLY WIRED ✓ (01:56
> save — S from SW4 A, D to +BATT, G to BT1 (−)):**
> the U7/U8 combo protects overcharge / overdischarge / overcurrent, **but NOT a reversed
> cell** — plugged backwards, the TP4056, DW01A and everything else get forward-biased body
> diodes and die. **Q1 = Si2305CDS** (≈55 mΩ, best of the two placed): source = SW4 output,
> drain = +BATT, gate = BT1 (−)/GND; correct polarity: Vgs < 0 → on, **zero drop**; reversed:
> gate = +3.7 V vs source → off, body diode blocks. **Remaining: REMOVE Q2 (Si2301CDS @
> (170.18, 63.50)) — a second P-FET on the battery rail has no role** (the DW01A/8205A
> already cover the cell side, and two series P-FETs only add RDSon drop).

#### LEDs + on/off button (decision 19-08-2026 — zero GPIO cost)

**Battery-status LEDs — free, from the TP4056 open-drain outputs** (the standard charger
circuit, active low) — **WIRED ✓ (01:18 save)**:

- **LED1 red = charging** ← `U6 (pin 7 CHRG)` via R6 (1 kΩ) — `LED1 K @ (46.99, 43.18) -> GND #PWR065` ✓
- **LED2 green = charged** ← `U6 (pin 6 STDBY)` via R7 (1 kΩ) — `LED2 K @ (46.99, 48.26) -> GND` ✓
- Both lit only while USB is plugged in — exactly when you're looking at them. 0 mA
  battery drain, 0 GPIOs.

**"Power" LED — deliberately NOT a rail LED.** An always-on 3.3V-rail LED burns ~2 mA
24/7 ≈ 48 mAh/day ≈ 5 % of the 1000 mAh budget. Instead:

- **D3 ← U1 (pin 52 NETLIGHT)** via 1 kΩ — the modem's built-in network-status blinker
  (off = modem off, 64 ms-on/800 ms-off = not registered, 64 ms-on/3 s-off = registered,
  64 ms-on/300 ms-off = data). Tells you the tracker is alive *and* connected. 0 firmware,
  0 GPIOs. (If size is a fight, this is the one to cut.)

**On/off button — a slide switch, PLACED as SW4 @ (38.10, 31.75), in the `BT1 (+)` path**,
between `U6 (pin 5 BAT)` and the rest of the board. `BT1 (+) -> SW4 pin 2`, `SW4 pin 1
(A) -> Q1 (pin 2 S) -> Q1 (pin 3 D) -> +BATT` and **`Q1 (pin 1 G) -> BT1 (−)` all wired ✓
(01:40 → 01:56) — the chain is fully closed**. Rationale:

- Every GPIO is allocated — no push-button input available without dropping a feature
  (e.g. the PWRKEY cap already ate the last one).
- CHIP_EN (SW2) can't be a soft power button: it *resets* the chip, it's not readable.
- A slide switch = true hard-off (µA-level leakage = none), works for storage/transport,
  and charging still works with it off (TP4056 BAT connects directly to the cell).
- Soft off stays as firmware: deep sleep + motion wake (U2 INT1), modem rail cut by
  GPIO19/EN, PWRKEY auto-on cap restarts the modem on wake.
- Part: tiny SMD slide switch (e.g. `Switch:SW_SPDT` 2.5 mm pitch, ~$0.20) — or a 2-pad
  solder jumper if the case can't fit a switch (open = off, storage only).

> **⚠ Status-LED logic (18-08-2026, user spec) — OPEN, needs a GPIO decision:**
> "display charge status on the LED while charging; when not charging, display only when
> the status button is pressed." That means the **ESP drives D1 (WS2812) from the TP4056
> state** — so CHRG (U6 pin 7, open-drain active-low) and/or STDBY (pin 6) must come in as
> **ESP inputs** (pull-ups to 3.3V), not just drive D1/D2. Charging = USB plugged = power
> is abundant, so the ESP can stay awake for the whole charge cycle; CHRG going low is also
> a perfect deep-sleep wake source (EXT1). **Problem: every GPIO is allocated.** Options:
>
> - **A (recommended): drop Y2 (32.768 kHz crystal + C8/C9)** → frees **GPIO0 = CHRG**,
>   **GPIO1 = STDBY** (U5 pins 4/5 XTAL_32K_P/N). Cost: RTC timer drifts ~±5 %/day in deep
>   sleep instead of ±20 ppm — but every GNSS fix re-syncs it and a panic beacon re-syncs
>   constantly; BOM gets smaller (crystal + 2 caps out). If "charge complete" showing
>   matters, wire both; one GPIO (CHRG only) works with "stops at full".
> - **B: keep Y2 and the LED1/LED2 CHRG/STDBY LED pair** — charging state shown by the free
>   LEDs; D1 (WS2812) stays button-only. **IN EFFECT ✓ — LED1/LED2 + R6/R7 wired on li-ion
>   (01:18 save)** — the ESP-input variant (A) would duplicate this.
> - **C: sacrifice the buzzer** — PWRKEY keeps its auto-on cap, GPIO3 = CHRG input instead
>   of BZ1. Loses the alarm piezo.
>
> ⚠ The TP4056 D1/D2 LED plan (rows above) applies only to option B — drop those two LEDs
> in A or C (CHRG/STDBY go straight to the ESP with 10 kΩ pull-ups).

### modem_rail

> **⚠ Two issues to fix before wiring (scan 19-08-2026):**
>
> 1. **U4 (pin 7 VIN) @ (45.72, 22.86) is touching a `3.3V` symbol** — the boost is fed from
>    the ME6211 LDO rail (500 mA max), which cannot feed ~2 A TX bursts, and cascading
>    LDO→boost wrecks efficiency. VIN must go to `BT1 (+) pin`.
> 2. **U4 (pin 3 VOUT) @ (60.96, 30.48) is wired to a `5V` symbol @ (62.23, 30.48)** —
>    SIM7670G `VBAT` max is **4.3 V**; 5 V would kill the modem. VOUT must be the
>    ~4.0 V `U1 VBAT pins 55–57` net (via the FB divider below), so relabel that 5V symbol `U1 VBAT pins 55–57`
>    and rewire FB, or the modem rail is wrong topologically AND electrically.

| Wire | Notes |
| ---- | ----- |
| `U4 (pin 7 VIN) -> BT1 (+) pin` | ⚠ **currently on 3.3V — move the symbol**; + 22 µF decap [ADD] |
| `U4 (pin 2 SW) -> L2 (pin 1)` | [ADD] 1 µH; `L2 (pin 2) -> BT1 (+) pin` |
| `U4 (pin 3 VOUT) -> U1 VBAT pins 55–57` | ⚠ **currently labeled 5V — relabel to `U1 VBAT pins 55–57` (4.0 V)**; + 22 µF decap [ADD] |
| `U4 (pin 4 FB) -> R_FB_HI (pin 1)` | [ADD] ~700 kΩ; `R_FB_HI (pin 2) -> U4 (pin 3 VOUT)`; `U4 (pin 4 FB) -> R_FB_LO (pin 1)` [ADD] 100 kΩ; `R_FB_LO (pin 2) -> GND` → VOUT = 0.5 × (1 + 700/100) = **4.0 V** (R3/R4 are taken on brains — FB divider gets its own role names) |
| `U4 (pin 5 EN) -> U5 (pin 26 GPIO19)` | C3 turns the modem rail off in deep sleep |
| `U4 (pin 6 MODE) -> BT1 (+) pin` | PFM for light-load idle (PROPOSED; GND = forced PWM) |
| `U4 (pin 1 GND) -> GND` | |

### network_and_gps

| Wire | Notes |
| ---- | ----- |
| `U1 (pin 55/56/57 VBAT) -> U1 VBAT pins 55–57` | + 22 µF + 100 nF [ADD] — the ~2 A TX burst lives here |
| `U1 (pin 9 TXD) -> U5 (pin 27 U0RXD)` | AT UART |
| `U1 (pin 10 RXD) -> U5 (pin 28 U0TXD)` | AT UART |
| `U1 (pin 1 PWRKEY) -> U5 (pin 8 GPIO3)` | pulse low ≥ 500 ms to power on/off |
| `U1 (pin 66 STATUS) -> U5 (pin 25 GPIO18)` | modem status sense |
| `U1 (pin 52 NETLIGHT) -> R_LED3 -> D3` | **DECIDED** network-status LED, 1 kΩ, D3 cathode side — see LEDs decision above |
| `U1 (pin 51 VBAT_ADC) -> U5 (pin 6 GPIO2)` | battery sense (alternate: resistor divider from BT1 (+) pin) |
| `U1 (pin 30 USIM1_VDD) -> J1 (VCC)` | + 100 nF decap |
| `U1 (pin 31 USIM1_DATA) -> J1 (I/O)` | |
| `U1 (pin 32 USIM1_CLK) -> J1 (CLK)` | |
| `U1 (pin 33 USIM1_RST) -> J1 (RST)` | |
| `U1 (pin 34 USIM1_DET) -> J1 (CSW or DSW)` | SIM-presence sense (optional) |
| `J1 (GND) -> GND`, `J1 (SH) -> GND` | |
| `U1 (pin 60 RF_ANT) -> U.FL (center)` | [ADD] U.FL + LTE flex antenna, 50 Ω trace |
| `U1 (pin 90 GNSS_ANT) -> GNSS patch (feed)` | [ADD] passive 25×25 mm patch, ground plane under it |
| `U1 (pin 97 1V8_GNSS) -> 1 µF decap` | [ADD] |
| `U1 (pin 99 VDD_2V8) -> 1 µF decap` | [ADD] |
| `U1 (pin 98 GNSS_PWRCTL) -> 3.3V` | GNSS always on while the modem rail is up (PROPOSED) |
| `U1 (pin 100 1PPS)` | [optional] leave NC |
| `U1 (pin 95 GNSS_TXD)` / `U1 (pin 96 GNSS_RXD)` | leave NC — NMEA comes over AT on UART0 (`AT+CGNSSINFO`) |
| `U1 (pin 116 GNSS_VBKP)` | leave NC (no backup cell) |
| `U1 GND pins (39×) -> GND` | ✓ **done 19-08-2026** — all 39 stubs terminated with GND symbols (pins 2, 17, 18, 29, 39, 45, 46, 54, 58, 59, 61, 62 left / 63–65, 69–89, 91, 92, 94 right) |

### accelerometer

| Wire | Notes |
| ---- | ----- |
| `U2 (pin 9 Vdd) -> 3.3V` | ✓ power symbol touches the pin @ (39.37, 24.13); + 100 nF [ADD] |
| `U2 (pin 10 Vdd_IO) -> 3.3V` | |
| `U2 (pin 6 GND) -> GND` | ✓ power symbol touches the pin @ (39.37, 49.53) |
| `U2 (pin 4 SDA/SDI) -> U5 (pin 14 GPIO8)` | I2C data — ⚠ **add R_SDA 4.7 kΩ pull-up to 3.3V [ADD]** (required for I2C; also straps GPIO8 high — GPIO8 is ignored at boot when GPIO9 is high, per Espressif) |
| `U2 (pin 1 SCL/SPC) -> U5 (pin 15 GPIO9)` | I2C clock — R_BOOT 10 kΩ doubles as the SCL pull-up ✓; firmware must use **open-drain** I2C (push-pull + reset mid-transaction → download mode) |
| `U2 (pin 3 SA0/SDO) -> GND` | I2C address 0x19 (or 3.3V = 0x18) |
| `U2 (pin 2 CS) -> 3.3V` | I2C mode (CS inactive) |
| `U2 (pin 12 INT1) -> U5 (pin 10 MTDI = GPIO5)` | wake-on-motion — RTC GPIO, deep-sleep EXT1 wake |
| `U2 (pin 11 INT2)` | leave NC |
| `U2 (pin 5 RES)` | leave NC (internal pull-up) |

### radio_layout

| Wire | Notes |
| ---- | ----- |
| `U5 (pin 1 LNA_IN) -> L1 (pin 1)` | cross-sheet net from brains |
| `L1 (pin 2) -> C1 (pin 1)` **and** `L1 (pin 2) -> E1 (pin 1)` | feed node |
| `C1 (pin 2) -> GND` | |
| `E1 (GND pads) -> GND` | antenna ground + keep-out |

### sound (new sheet, 19-08-2026 — DECIDED: passive piezo + LEDC melodies)

| Wire | Notes |
| ---- | ----- |
| `U5 (pin 8 GPIO3) -> BZ1 (pin 1 +)` | direct GPIO drive, **no transistor** — LEDC PWM square wave (2–4 kHz, 50% duty) plays real melodies (pitch + rhythm); bit-banged GPIO toggling works too (same wiring) |
| `BZ1 (pin 2 −) -> GND` | |
| (optional) 100 nF across BZ1 | only for the active-buzzer variant; not needed for the piezo |

Buzzer decision (19-08-2026):

- **Swap BZ1 for a passive piezo transducer: Murata PKLCS1212E4001-R1 (12×12×2 mm, ~75 dB,
  2 g) or PKLCS0909E4001-R1 (9×9×1.9 mm — the tiny pick).** The placed SparkFun 9 mm symbol
  is an *active* buzzer (fixed ~2.7 kHz, 20–30 mA → needs a driver transistor, rhythm-only
  "melodies"). The passive piezo: ~2 mA, direct GPIO, and the ESP32-C3's LEDC plays
  pitch-accurate melodies (find-me pattern, low-battery tone, SOS).
- Symbol: keep the 2-pin SparkFun symbol as a placeholder or use `Device:Buzzer`; footprint
  = the Murata PKLCS pad layout.

> **⚠ OPEN conflict (23:02 save):** the brains sheet routes GPIO3 to a
> `power-on_pulse` hierarchical label (modem PWRKEY) instead of the buzzer. The sound sheet
> has the piezo **LS1 (PKLCS1212E4001-R1) wired** to `Buzzer+`/`Buzzer-` labels @ (35.56,
> 20.32/22.86) ✓ (6 wires) — **but brains still has no `Buzzer+`/`Buzzer-` labels**, so the
> buzzer nets dead-end on sound. Either keep the PWRKEY cap (100 nF) and route GPIO3 → the
> buzzer (add the two labels on brains), or drop the buzzer decision and keep GPIO3 → PWRKEY
> (then LS1 gets no GPIO — remove it).

- Wiring: `U5 (pin 8 GPIO3) -> BZ1 (+)`, `BZ1 (−) -> GND`. GPIO3 is freed by the PWRKEY
  auto-power-on cap (see GPIO table). No firmware worry about SCL/GPIO9 — the buzzer is on
  GPIO3 (pin 8), not on the strapping pin.
- Modem-codec alternative (only if loud + melodic matters): SIM7670G EAR/MIC pins could ring
  tones via AT — overkill, modem must be awake (100 mA class).

## Components to add (summary)

| [ADD] | Value | Where it plugs |
| ----- | ----- | -------------- |
| Decaps ×7 | 100 nF 0402 | U5 pins 2, 11, 17, 18, 31; U2 pin 9; U1 pin 30 |
| C3 (C_IN) | 1 µF 0402/0603 | U3 pin 1 (V_IN) — ME6211 input cap — **DONE ✓ (19:37 save)** |
| C4 (C_OUT) | 10 µF 0603 | U3 pin 5 (V_OUT, 3.3 V rail) — LDO stability + bulk — **DONE ✓ (19:37 save)** |
| C5 (C_OUT 100nF) | 100 nF 0402 | U3 pin 5 (V_OUT) — **DONE ✓ (19:37 save)** |
| C2 (C_VBUS) | 10 µF 0603 (recommended) | J2 VBUS side — **DONE ✓ (19:37 save)** |
| Bulk caps ×2 | 22 µF 0603/0805 | U4 pin 7 (VIN), U4 pin 3 (VOUT) |
| Modem bulk | 22 µF + 100 nF | U1 pins 55/56/57 (VBAT) |
| Small decaps | 1 µF ×2 | U1 pin 97 (1V8_GNSS), U1 pin 99 (VDD_2V8) |
| R_UART_A/B | 1 kΩ ×2 0402 | in series on J3/TP side of U0TXD + U0RXD — programmer backfeed/reversed-plug protection, **DECIDED [ADD]** |
| Q2 | P-MOSFET SOT-23 (SI2301/SI2305 class, ~$0.05) | reverse-polarity protection (zero drop), **DECIDED — PLACED as Q1 = Si2305CDS @ (57.15, 31.75) rot 90; FULLY WIRED ✓ (gate included, 01:56 save)** |
| R_EN | 10 kΩ 0402 | U5 pin 7 -> 3.3V — **DONE ✓ (R5)** |
| R_SDA | 4.7 kΩ 0402 | U5 pin 14 (GPIO8/SDA) -> 3.3V — I2C pull-up + straps GPIO8 high, **DECIDED** — **placed as R4 (0603) on brains but ⚠ wired in series, not to 3.3V — needs rewire** |
| R_BOOT | 10 kΩ 0402 | U5 pin 15 -> 3.3V — **DONE ✓ (R3)** (doubles as I2C SCL pull-up) |
| SW_RST | tact switch SMD 4.5×4.5 mm | U5 pin 7 (CHIP_EN) ↔ GND — **DONE ✓ (SW2)** |
| SW_BOOT | tact switch SMD 4.5×4.5 mm | U5 pin 15 (GPIO9) ↔ GND — **DONE ✓ (SW1)** |
| J3 | 1×6 pin 2.54 mm THT header (or 6 test pads) | 3V3, GND, U0TXD (pin 28), U0RXD (pin 27), CHIP_EN (pin 7), GPIO9 (pin 15) |
| R8 (PROG) | 1.2 kΩ 0603 (2.4 kΩ = 500 mA if preferred) | U6 pin 2 -> GND — **PLACED ✓, WIRED ✓ (01:18 save) — 1.2 kΩ = 1 A charge rate** |
| FB divider R_FB_HI/R_FB_LO | 700 kΩ / 100 kΩ 0402 | U4 VOUT -> FB -> GND (4.0 V rail) — ⚠ the 5V symbol on U4 VOUT must become U1 VBAT (4.0 V) first |
| L2 | 1 µH 0603 | U4 pin 2 SW <-> BT1 (+) pin |
| U8 | 8205A SOT-23-6 (BMS toggle — unpopulated by default) | U7 gates, see li-ion sheet — **PLACED @ (162.56, 44.45); symbol RESTORED 02:28 (fresh SnapEDA) + pin 5 SPLIT from pin 2 by the user 02:47 (two separate drains); real part: 1=S1, 2=D, 3=S2, 4=G2, 5=D, 6=G1** |
| Q1 | Si2305CDS P-MOSFET SOT-23 | reverse-polarity protection, **FULLY WIRED ✓ (S = SW4 A, D = +BATT, G = BT1 (−), 01:56 save)** |
| Q2 | Si2301CDS P-MOSFET SOT-23 | **PLACED @ (170.18, 63.50), unwired — no role; recommend REMOVE (Q1 covers reverse protection)** |
| PWRKEY cap | 100 nF 0402 | U1 pin 1 PWRKEY -> GND — auto-power-on (SIM7670G app-note wiring), **frees GPIO3 for the buzzer — DECIDED, [ADD]** |
| BZ1 | passive piezo, Murata PKLCS1212E4001-R1 (12×12×2 mm) or PKLCS0909E4001-R1 (9×9×1.9 mm) | U5 pin 8 (GPIO3) -> (+) -> (−) -> GND — **replace the SparkFun 9 mm active buzzer** |
| D1, D2 | red + green LED 0402/0603 | U6 pin 7 (CHRG) / pin 6 (STDBY) — charging / charged, **DECIDED** |
| D3 | LED 0402/0603 | U1 pin 52 (NETLIGHT) — network-status blinker, **DECIDED** |
| R_LED1/2/3 | 1 kΩ ×3 0402 | in series with D1/D2/D3 |
| D1 | WS2812B-2020 status LED (2×2×0.75 mm, 1-wire addressable) — was "D4" in this doc | U5 pin 16 (GPIO10) -> DI; VDD -> 3.3V (pin-coincident ✓, ⚠ power question — see visual section); VSS -> GND; DOUT -> no-connect; + 100 nF at LED — info/status colors, **DECIDED, PLACED ✓ (visual sheet)** |
| SW4 (on/off slide switch) | tiny SMD slide switch SPDT (`SPDT_Slide_SMD_6.7x2.6mm` — pins 1 A / 2 2 / 3 C) | in BT1 (+), between U6 pin 5 (BAT) and the load — hard on/off, **PLACED ✓ @ (38.10, 31.75); `BT1 (+) -> SW4 pin 2` and `SW4 pin 1 (A) -> Q1 S` wired ✓ (01:40)** |
| SW3 (status button) | tact switch SMD 4.5×4.5 mm (same as SW1/SW2) | U5 pin 9 (MTMS = GPIO4) ↔ GND — status button (short = battery color on D1, long = BLE pairing), **DONE ✓ (19:37 save)** |
| Jumper | 2-pad solder jumper (bridged) | P_NEG <-> GND |
| U.FL + antenna | U.FL + LTE flex | U1 pin 60 |
| GNSS patch | 25×25 mm passive | U1 pin 90 |
| Optional | NTC, 1PPS, ESD USBLC6-2SC6, extra status LED on GPIO18/STATUS | per rows above |

## GPIO allocation (PROPOSED — all free GPIOs of the C3 QFN-32)

Pin references use the **symbol pin names** (`MTMS`, `MTDI`, `U0TXD`, …); the GPIO number
follows in parentheses where the name is a function name.

| U5 pin (name) | GPIO | Assigned to |
| ------------- | ---- | ----------- |
| 28 (U0TXD) | 21 | U1 pin 9 (TXD) |
| 27 (U0RXD) | 20 | U1 pin 10 (RXD) |
| 14 (GPIO8) | 8 | U2 pin 4 (SDA) — strapping pin, ignored at boot when GPIO9 is high; add R_SDA pull-up |
| 15 (GPIO9) | 9 | U2 pin 1 (SCL) — strapping pin, keep R_BOOT pull-up |
| **9 (MTMS)** | **4** | **SW3 status button (RTC GPIO → EXT1 deep-sleep wake) — DONE ✓** |
| **10 (MTDI)** | **5** | **U2 pin 12 (INT1, motion wake) — MOVED from GPIO10: MTDI is RTC, GPIO10 is not** |
| 6 (GPIO2) | 2 | U1 pin 51 (VBAT_ADC) — strapping pin, OK as ADC input |
| 8 (GPIO3) | 3 | **BZ1 (buzzer, LEDC melody)** — freed by the PWRKEY auto-power-on cap |
| 25 (GPIO18) | 18 | U1 pin 66 (STATUS) — ⚠ SIM7670G STATUS is **1.8 V logic**; 3.3 V GPIO won't read it high → level-shift (2N7002 + 10 kΩ pull-up) or drive an LED from STATUS instead |
| 26 (GPIO19) | 19 | U4 pin 5 (EN, modem rail) |
| 16 (GPIO10) | 10 | **D1 WS2812B-2020 info LED (1-wire color status) — PLACED ✓ on visual sheet** — was spare; not RTC-capable (fine, LED doesn't need wake) |

> **Buzzer GPIO — DECIDED (19-08-2026): U5 pin 8 (GPIO3), freed by the 100 nF PWRKEY
> auto-power-on cap.** The cap is a documented SIM7670G wiring (delayed self-hold on the
> internal pull-up) and the modem rail is already gated by U5 pin 26 (GPIO19)/EN, so PWRKEY
> loses nothing. Firmware plays melodies on GPIO3 with LEDC PWM (passive piezo, 2–4 kHz
> square wave) — pitch + rhythm, no transistor.
>
> **Status button SW3 — DECIDED (19-08-2026), DONE ✓ (19:37 save): U5 pin 9 MTMS (= GPIO4), button → GND,
> internal pull-up.** RTC-capable → EXT1 wake from deep sleep. **The button drives the
> LED (D1), not the buzzer** — click taxonomy (firmware, all on existing hardware):
>
> - **short click** → battery status as a **color on D1** (red <25 % / yellow ~50 % /
>   green ~2/3 / cyan full), measured via U1 pin 51 (VBAT_ADC) → U5 pin 6 (GPIO2).
> - **long click (≥3 s)** → BLE pairing mode (Find My / Find My Device registration window,
>   D1 flashes the pairing pattern).
> - The piezo (GPIO3) is NOT part of the button logic — it keeps its own alerts
>   (alarm/melodies triggered by motion events or firmware events only).
> - **D1 (WS2812B-2020, visual sheet) on U5 pin 16 (GPIO10) shows the colors** — battery
>   level color, pairing flash, modem-heartbeat blink. 1-wire protocol driven by the C3's
>   RMT peripheral; GPIO10 is not a strapping pin, so a boot-time low on the data line is
>   harmless. ⚠ needs the `DIN` label on brains (GPIO10 row); power question — see visual
>   section.
>
> **Visual sheet (23:52 save) — D1 WS2812B-2020 status LED @ (43.18, 27.94):** all
> connections wired (8 wires), C14 (100 nF) decap across VDD/VSS ✓:
>
> - **`+BATT` power symbol (#PWR071, Value = "+BATT") @ (19.05, 20.32)** — wire —
>   `C14 pin 1 @ (21.59, 30.48)` — wire — `D1 VDD @ (43.18, 20.32)` ✓
> - `GND (#PWR072) @ (19.05, 39.37)` — wire — `C14 pin 2 @ (21.59, 35.56)` — wire —
>   `D1 VSS @ (43.18, 35.56)` ✓
> - `LED_DIN` label @ (34.29, 27.94) — wire — `D1 DIN @ (35.56, 27.94)` ✓
> - `D1 DOUT @ (50.80, 27.94)` — no-connect flag ✓ (single LED, no daisy chain)
>
> **✓ +BATT source RESOLVED (00:30 save):** the user placed a matching `+BATT` power symbol
> on the li-ion sheet — `+BATT #PWR067 @ (66.04, 29.21)`, wired to **U6 pin 5 BAT** ✓ (the
> charger's battery node, downstream of SW4). Honest note: electrically U6 pin 5 BAT is the
> SAME node as the cell's + pin (the TP4056 is a charger — its BAT pin goes straight to
> the cell via SW4; it does NOT regulate). If you want the rail truly hardened against the
> cell: the P-FET reverse-polarity protection now placed as **Q1 = Si2301CDS @ (43.18, 39.37)**
> (BT1 (+) → Q1 → rail, gate → BT1 (−), still unwired) turns `+BATT` into the protected
> system rail. If you want REGULATED brightness instead: `+BATT` → drop it, keep D1 VDD on
> 3.3V (U3 VOUT, what the sheet had before) — dimmer but constant.
>
> **⚠ Motion-wake correction (19-08-2026):** the original INT1 → U5 pin 16 (GPIO10) plan
> could NOT wake the C3 from deep sleep — only RTC GPIOs (GPIO0–5) do EXT1 wake, and
> GPIO10 is not one. INT1 now goes to **U5 pin 10 MTDI (= GPIO5, RTC)**, so EXT1 wakes on
> motion AND button. MTMS/MTDI (GPIO4/5) are the JTAG pins — using them as GPIO disables
> hardware JTAG debugging; flashing stays UART-based via J3, which was the plan anyway.

## Progress assessment (19-08-2026, 01:18 save)

**Overall: ~75 % of the schematic is electrically final.** brains is at **97 wires**; the
li-ion sheet jumped from 4 to **28 wires** (01:18 save) — charger fully wired (VCC, CE,
BAT→+BATT, PROG→R8, GND+EPAD), CHRG/STDBY LED pair wired (LED1/LED2 + R6/R7), TEMP→TP7.
Remaining on li-ion: U7 VCC → BT1 (+), the U8 drains (pin 2 → cell −, pin 5 → P_NEG),
the jumper itself, Q2 removal, and the R9/TP8/TP9 stub. What's left overall is the short
list below — the biggest piece of real wiring work
is still modem_rail.

**Done & verified (23:02 → 01:18 saves):**

- **LED data line CLOSED ✓** — brains `LED_DIN` @ (78.74, 57.15) wired to GPIO10, visual
  `LED_DIN` @ (34.29, 27.94) wired to D1 DIN — one net, two sheets
- **+BATT rail SOURCED ✓** — `+BATT #PWR067 @ (66.04, 29.21)` on li-ion, wired to U6 pin 5
  BAT; visual's `+BATT #PWR071` consumes it
- **Annotation (01:56)** — **Q1 (pin 1 G) → BT1 (−) WIRED ✓ (gate chain complete)**; NEW
  `-BATT` symbol added on the BT1 (−) rail; li-ion power symbols renumbered: -BATT
  #PWR072→#PWR064, GND #PWR064→#PWR065, GND #PWR065→#PWR066, +BATT #PWR066→#PWR067,
  3.3V #PWR067→#PWR068, 5V #PWR068→#PWR069, GND #PWR069→#PWR070; visual:
  +BATT #PWR070→#PWR071, GND #PWR071→#PWR072
- **Annotation (01:18)** — visual: +BATT #PWR069→#PWR070, GND #PWR068→#PWR071; li-ion:
  GND #PWR071→#PWR064, GND #PWR070→#PWR065, +BATT #PWR069→#PWR066, 3.3V #PWR066→#PWR067,
  5V #PWR064→#PWR068, GND #PWR065→#PWR069; resistors renumbered: R6/R7 = 1 kΩ (LED pair),
  R8 = 1.2 kΩ (PROG), R9 = 10 kΩ (stub); Q1 moved to (57.15, 31.75) as Si2305CDS, Q2 to
  (170.18, 63.50) as Si2301CDS
- **8205A pins 2+5 SPLIT (02:47–02:48, user)** — pin 5 moved to (2.54, 7.62), visible;
  pin 2 nudged to (-1.27, 7.62); U8 re-placed on the sheet (02:48 save). New tips: pin 2
  (161.29, 36.83), pin 5 (165.10, 36.83). Drains are now two separate nets — the
  bare-cell BMS wiring is expressible. OD→pin 6, OC→pin 4, CS→pins 1+3 unchanged ✓
- **8205A symbol RESTORED + OD fixed (02:28–02:30)** — user re-downloaded the SnapMagic
  8205A from SnapEDA (fresh zip @ 02:27; the other model had corrupted the pin names):
  restored symbol is anonymous (no pin names), pin 5 hidden at pin 2's spot (drains
  merged → one net; ⚠ blocks the bare-cell mode, fine for bridged default). **U7 OD moved
  from U8 pin 2 → pin 6 (real G1) — DONE ✓ (02:30 save, wire @ (140.97, 39.37) →
  (140.97, 46.99) → (152.40, 46.99))**; OC → pin 4 (real G2) and CS → pins 1+3 unchanged ✓
- **8205A pinout verified against UMW datasheets (02:08)** — real UMW 8205A =
  1=S1, 2=D, 3=S2, 4=G2, 5=D, 6=G1; the pre-restore symbol labels were wrong on pins
  2/4/5/6
- **li-ion charger wired ✓ (01:18 save, 28 wires)** — 5V→VCC, CE→3.3V, BAT→+BATT,
  PROG→R8 (1.2 kΩ, = 1 A — swap for 2.4 kΩ if 500 mA preferred)→GND, GND pin 3 + EPAD→GND,
  CHRG→R6→LED1 (red)→GND, STDBY→R7→LED2 (green)→GND, TEMP→TP7
- **Annotation (01:18)** — visual: +BATT #PWR069→#PWR070, GND #PWR068→#PWR071; li-ion:
  GND #PWR071→#PWR064, GND #PWR070→#PWR065, +BATT #PWR069→#PWR066, 3.3V #PWR066→#PWR067,
  5V #PWR064→#PWR068, GND #PWR065→#PWR069; resistors renumbered: R6/R7 = 1 kΩ (LED pair),
  R8 = 1.2 kΩ (PROG), R9 = 10 kΩ (stub); Q1 moved to (57.15, 31.75) as Si2305CDS, Q2 to
  (170.18, 63.50) as Si2301CDS
- **Sound sheet wired ✓** — LS1 (PKLCS1212E4001-R1 piezo) on `Buzzer+`/`Buzzer-` labels
  (6 wires) — ⚠ brains still has no Buzzer labels (GPIO3 conflict stays OPEN)
- **R4 pull-up COMPLETE ✓** — `3.3V #PWR055 @ (71.12, 50.8)` → `R4 pin 2 @ (73.66, 50.8)`,
  `R4 @ (77.47, 50.8) rot 90` → `pin 1 @ (81.28, 50.8)` → `I2C (SDA)` label + GPIO8
- **I2C (SCL) FIXED ✓** — label @ (44.45, 57.15), stub down to the GPIO9 row → GPIO9
- **Cross-sheet label names ALL MATCH ✓** — `UART (TXD)`/`UART (RXD)`, `power-on_pulse`,
  `battery_voltage_sense`, `Motion_wake`, `modem_status`, `modem_rail_switch`
- **GPIO18/19 connected ✓ (label anchors on the pin tips)**
- J3 (6/6) + TP1–TP6 ✓ | SW1/SW2/SW3 ✓ | power_input ✓ (30 wires) | visual: D1 ✓

**Fix before continuing (4 flags):**

1. **R_UART_A/B (2× 1 kΩ series) NOT placed yet** — between U5 pins 27/28 and the
   J3/label junction (see ASCII §7) — R6/R7 are taken by the li-ion LEDs; will be R10/R11
2. **GPIO3 conflict OPEN:** PWRKEY pulse vs buzzer (LS1 wired on sound, needs
   `Buzzer+`/`Buzzer-` labels on brains if the buzzer wins)
3. modem_rail untouched: U4 VIN on 3.3V + VOUT labeled 5V (kills the modem at 4.3 V max)
4. Charge-status logic: **option B (LED1/LED2 pair) is now WIRED on li-ion ✓** — the
   ESP-input variant (A: drop Y2 → GPIO0/1 = CHRG/STDBY) would duplicate it; drop the
   GPIO plan unless "charge complete" must wake the ESP

**Li-ion next steps (open wiring):** **U7 VCC → TP11 only (DW01A unpowered — extend to
BT1 (+))**; **U8 drains: pin 2 → cell − (BT1 (−)), pin 5 → P_NEG → jumper → GND** (jumper
not placed yet; ✓ drains now separate pins since 02:47); **Q2 (Si2301CDS) — no role,
recommend REMOVE**; R9/TP8/TP9 stub (battery-sense divider or delete?); R8 1.2 kΩ = 1 A
charge rate (2.4 kΩ = 500 mA if preferred). Q1 gate → BT1 (−) **DONE ✓ (01:56)**; OD → pin 6 **DONE ✓
(02:30)**.

**Biggest remaining blocks:** modem_rail (L2 + FB divider + caps), U1 & J1 wiring on
network_and_gps (39 GND stubs done), accelerometer wiring, sound LS1 (needs brains labels

- GPIO3 call), li-ion BMS-toggle + SW4→Q1→+BATT chain.

**Risk watch:** modem_rail (item 3) is the only electrical show-stopper if it ships as-is;
everything else is connectivity hygiene.

## Battery / energy storage verdict (density-first)

- **Li-ion pouch stays**: 200–260 Wh/kg — nothing rechargeable beats it for size.
- **Chosen cell: 603050-class, 1000 mAh** — sized for the client spec: WCCS (cat
  always moving) at 2-min GNSS fixes ≈ 24 h in the palm-capsule class; static =
  15-min heartbeat. Firmware implements the adaptive ladder (BLE beacon always on +
  motion-gated 2-min fixes + low-battery cadence stretch). Form factor pending
  (603050/603048 slab ↔ pendant puck vs 102050 stick ↔ strap capsule) — either
  cell works on the same circuit.
- LiFePO4 / LTO: safer but ~2× worse density; 3.2 V nominal eats the 3.3 V LDO
  margin. Supercaps: only as a *later* parallel burst helper, never the main store.
- **Charging dock:** contacts + pogo pins in a case (AirPods-case style), not Qi —
  Qi needs a ≥ 20 mm coil ≈ the whole board.

## Part sourcing notes (KiCad vs SnapMagic)

| Part | Source | Reference |
| ---- | ------ | --------- |
| U.FL / IPEX | KiCad | `Device:Antenna` symbol; footprint `Connector_Coaxial:U.FL_Hirose_U.FL-R-SMT-1_Vertical` |
| Solder jumper | KiCad | `Jumper:SolderJumper-2_P1.3mm_Bridged_Pad1.0x1.5mm` (ships bridged) |
| Battery symbol | KiCad | `Device:Battery_Cell` (BT1, placed) |
| Tact switches | KiCad | `Switch:SW_Push` symbol; `Button_Switch_SMD:SW_SPST_TL3342` (4.5×4.5 mm) or similar SMD tact |
| Programming header | KiCad | `Connector_PinHeader_2.54mm:PinHeader_1x06_P2.54mm_Vertical` (J3) |
| 8205A dual MOSFET | SnapMagic (SnapEDA) | UMW 8205A, SOT-23-6, ~$0.07 |
| LTE flex antenna | SnapMagic / Ali | Taoglas FXUB63 family; U.FL pigtail variants at LCSC/Ali |
| GNSS patch | SnapMagic / LCSC | 25.4×25.4×4 mm passive, e.g. Taoglas CGGBP.25.4.A.02 |
| 2450AT18A0100001E (BLE) | already integrated | lib `2450AT18A0100001E`, E1 placed |

Search SnapMagic with the MPN (`U.FL-R-SMT-1`, `8205A UMW`, `FXUB63`,
`CGGBP.25.4.A.02`) and pick the entry whose package picture matches; prefer
entries with symbol+footprint+3D.
