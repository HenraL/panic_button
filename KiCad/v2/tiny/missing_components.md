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
-- LAST Modified: 19:20:52 20-08-2026
-- DESCRIPTION:
-- A small panic beacon: reports its position to a server, falls back to SMS when unreachable, and advertises BLE (Find My-style) so phones can interact with it without an app.
-- /STOP
-- COPYRIGHT: (c) Asperguide
-- PURPOSE: This file lists every component the schematic still lacks, the symbol/footprint to use, and where each one links (pin numbers / nets).
-- // AR
-- +==== END AsperHeader =================+
-->
# Missing & present components — panic_button_tiny (as of 20-08-2026)

Complete wiring ledger, pin by pin. Verified against the saved schematic files
(embedded symbols, Y-flip applied). "PROPOSED" = sensible default, owner can
change; "OPEN" = decision pending.

> **Save state: 16:44 20-08-2026.** **Global annotation pass (all sheets share one
> numbering now) + FB divider WIRED ✓ + power-symbol swap (VBATT = #PWR054, -BATT = #PWR055)
> and REGRESSIONS FIXED (16:44: R3.2 re-routed to the FB node, C6 rewired VIN → GND).**
> Resistor map (old → new): R15→**R3** (47k
> RC0603FR-0747KL, modem_rail FB_LO), R14→**R4** (330k RC0603FR-07330KL, modem_rail
> FB_HI), R3→**R5** (10k_0402, brains GPIO9 pull-up), R4→**R6** (4.7k_0603, brains SDA
> pull-up), R5→**R7** (10k_0402, brains CHIP_EN pull-up), R6→**R8** (470k, li-ion OD
> pull-up), R7→**R9** (470k, li-ion OC pull-up), R8→**R10** (1k, li-ion CHRG LED),
> R9→**R11** (1k, li-ion STDBY LED), R10→**R12** (2.4k value, li-ion PROG),
> R11→**R13** (10k, li-ion stub), R12→**R14** (1k, uart U0TXD series), R13→**R15**
> (1k, uart U0RXD series). **FB divider DONE ✓: VOUT → R4 (330k) → FB → R3 (47k)
> → GND = 4.01 V (R3.2 re-routed to the FB row at 16:44, was on VOUT since 14:46).**
> **C6 FIXED ✓ (16:44): +BATT #PWR050 now feeds the VIN node directly; C6 = VIN → -BATT
> #PWR051 parallel decoupling** (was in series, blocking DC, since 13:14). UART swap FIXED ✓ (12:14). The `clock` and
> `uart` sheets connect to U5 via **hierarchical sheet pins on the sheet boxes inside
> brains** (not via labels on the brains canvas); clocks connected ✓, uart connected ✓
> (swap fixed). The pasted annotation log shows intermediate numbers; **the file is the
> truth** — this ledger quotes the saved state only.
>
> **Save state: 19:32 20-08-2026 (annotation pass, all sheets — clean ranges now:
> accelerometer #PWR042–043, radio_layout #PWR041, power_input #PWR044–048, modem_rail
> #PWR049–055, brains #PWR056–064, li-ion #PWR065–070, sound #PWR071, visual
> #PWR072–073, uart #PWR074–075, clock #PWR076–077).** modem_rail symbols also MOVED
> (nets re-verified ✓): +BATT #PWR050 (43.18,38.1), -BATT #PWR051 (46.99,20.32),
> GND #PWR052 (52.07,67.31), -BATT #PWR053 (57.15,64.77), VBATT #PWR054 (114.3,46.99),
> -BATT #PWR055 (127.0,57.15). **Design rule (user, 20-08-2026): the li-ion sheet uses
> WIRE connections only — no power symbols other than GND** (avoids an accidental
> feedback loop / spicy pillow). **DECIDED (19:5x): global labels on the CHARGING side
> only (the U8 block) — `+BATT` label @ U8 BAT junction, `5V` label @ U8 VCC, `3.3V`
> label @ U8 CE; the BATTERY side is pure wires (cell + chain BT1(+) → SW4 → Q1 → +BATT
> label point; cell − rail BT1(−) → U7, NO label/symbol). GND symbols stay. Consequence:
> modem_rail's `-BATT` symbols (#PWR051/#PWR053/#PWR055) lose their li-ion anchor — they
> are already on the GND node there (cell − = GND through U7) → rename them to `GND`
> (schematic change proposal, not applied yet).
>
> **21:5x: BMS → battery/charger RE-WIRED on the RESET li-ion1 (agent, verified — 0 mid-wire
> crossings this time):** U7 pin 2 (cell-side drain) → cell − rail (wires (44.45, 39.37) →
> (44.45, 44.45) → (60.96, 44.45)); U7 pin 5 (system-side drain) → new `GND #PWR071` @
> (93.98, 20.32) (wire (93.98, 26.67) → (93.98, 20.32)); U6 pin 5 VCC → `BT1 (+)` pre-SW4
> (wire (45.72, 30.48) → (54.61, 30.48) — TP1→TP3 gap bridge; the 20:2x route to (62.23,
> 30.48) crossed the TP4 rail and was reset by the owner). Net check ✓: BT1+/TP1/TP3/U6-VCC;
> BT1−/TP2/U6-GND/U7-2; U7-5/TP4/GND#PWR071. Symbols untouched (power symbols + PIN± kept).
> Backup: `bonus/tmp/li-ion_charging_handling1.kicad_sch.bak-reset-pre-wiring`.
> ⚠ KiCad was open during the edit — **reload li-ion1 in KiCad before saving** or the wires
> are clobbered. Still open: SW4/Q1 on-chain redo, toggle jumper pads [ADD], R8/R9.
>
> **20:06: WORKING COPY = `li-ion_charging_handling1.kicad_sch`** (duplicate of the
> li-ion sheet, both subsheets of power_input; KiCad re-annotated the duplicate — ref
> mapping in the li-ion section header). **Global-label conversion APPLIED on the
> duplicate:** `+BATT`/`3.3V`/`5V` power symbols → global labels (same anchor points,
> nets verified against wire endpoints), `-BATT` power symbol deleted (cell − = pure
> wires), redundant `PIN+(5V)`/`PIN-(5V)` labels deleted, GND symbols kept. Original
> sheet untouched. The DECIDED note above (U8/U7/BT1/…) describes the original's refs.

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
| `BT1 (+)` | battery positive | U11 pin 5 BAT (TP4056), U4 pin 7 VIN, U9 pin 5 VCC (DW01A) — cell +, a *net name* not a connector |
| `BT1 (−)` | battery negative (raw) | U10 pin 2 (cell-side drain), Q2 gate, toggle jumper pad 2 — the cell − rail; **pure wires on li-ion1, no symbol/label** (20:06) |
| `U1 VBAT pins 55–57` (net `U1 VBAT pins 55–57`) | ~4.0 V modem rail | U4 V_OUT (TPS61022) |
| `GND` | ground | everywhere |

## Present on the sheets

| Sheet | Ref | Part | Wiring |
| ----- | --- | ---- | ------ |
| brains | U5 | ESP32-C3 (QFN-32) | done ✓ (63 wires) |
| clock | Y1 | **32.768 kHz crystal (CTS3-32.768-12.5-20-R)** — re-ref'd 01:06 (was Y2) | done ✓ (25 wires) |
| clock | Y2 | **C3E-40.000-12-1010-X (40 MHz)** — re-ref'd 01:06 (was Y1) | done ✓ |
| clock | C13, C16 | 22 pF load caps (Y1 32K: XTAL_32K_N + XTAL_32K_P) | done ✓ |
| clock | C14, C15 | 22 pF load caps (Y2 40 MHz: XTAL_P + XTAL_N) | done ✓ |
| brains | C8–C11 | 100 nF decoupling caps (renumbered 00:28, were C10–C13) | done ✓ (pin 1 → GND, pin 2 → 3.3V) |
| brains | R5 | 10 kΩ GPIO9 pull-up (= R_BOOT, 10k_0402) | done ✓ (GPIO9 → 3.3V #PWR056) |
| brains | SW1 | tact switch — BOOT (GPIO9) | done ✓ (pin 2 → GPIO9, pin 1 → GND #PWR057, pin-touch) |
| brains | SW2 | tact switch — RST (CHIP_EN) | done ✓ (pin 1 → CHIP_EN junction, pin 2 → GND #PWR062) |
| brains | SW3 | tact switch — status button (**MTMS = GPIO5**) | done ✓ (pin 1 → U5 pin 9 MTMS tip (132.08, 57.15), pin 2 → GND #PWR064) |
| brains | R6 | 4.7 kΩ SDA pull-up | **done ✓** — `3.3V #PWR058 @ (71.12, 50.8)` → R6 pin 2 → pin 1 → `I2C (SDA)` label + GPIO8 |
| brains | R7 | 10 kΩ CHIP_EN pull-up (= R_EN, 10k_0402) | done ✓ (3.3V #PWR063 @ (180.34, 52.07) → pin 2, pin 1 → CHIP_EN junction) |
| uart | J3 | 1×6 UART programming header | done ✓ — all 6 pins wired (3V3, GND, U0TXD, U0RXD, CHIP_EN, GPIO9) |
| uart | R14, R15 | 1 kΩ series (R_UART pair, U0TXD/U0RXD) | done ✓ |
| uart | TP8–TP13 | test points (3V3, U0TXD, CHIP_EN, GND, U0RXD, GPIO9) | done ✓ — each wired to its J3 row |
| visual | D1 | WS2812B-2020 status LED | done ✓ (8 wires, D1 @ (43.18, 27.94), C12 100 nF across VDD/VSS, `+BATT #PWR072` on VDD, `LED_DIN` wired) |
| power_input | J2 | USB-C charge-only | done ✓ (32 wires) |
| power_input | U3 | ME6211C33M5 (3.3 V LDO) | done ✓ |
| power_input | R1, R2 | 5.1 kΩ CC pull-downs | done ✓ (CC2→R2→GND wired; CC1→R1→GND pin-coincident, see note) |
| li-ion_charging_handling1 (WORKING COPY — RESET 21:5x, refs = ORIGINAL's: U6/U7/U8/BT1/SW4/Q1) | U6 | DW01A protection | wired ✓ — VCC → BT1 (+) powered ✓ (21:5x), GND → U7 pin 2 on cell − rail ✓ (toggle-split pending) |
| li-ion_charging_handling1 (WORKING COPY — RESET 21:5x, refs = ORIGINAL's: U6/U7/U8/BT1/SW4/Q1) | U7 | 8205A protection FETs | wired ✓ — pin 2 on cell − rail ✓, pin 5 on GND ✓ (21:5x) |
| li-ion_charging_handling1 (WORKING COPY) | U11 | TP4056-42-ESOP8 charger | wired ✓ |
| li-ion_charging_handling1 (WORKING COPY) | BT2 | `Device:Battery_Cell` (1000 mAh) | wired ✓ (→ TP14/TP15 only; ⚠ on-chain open) |
| li-ion_charging_handling1 (WORKING COPY) | R16, R17 | 470 kΩ OD/OC pull-ups (BMS toggle) | ⚠ placed (13:14) but 0 wires — see section |
| li-ion_charging_handling1 (WORKING COPY) | R18, R19 | 1 kΩ LED resistors (CHRG/STDBY) | wired ✓ |
| li-ion_charging_handling1 (WORKING COPY) | R20 | 1.2 kΩ PROG (Value = 2.4 kΩ → 500 mA) | wired ✓ |
| li-ion_charging_handling1 (WORKING COPY) | R21 | 10 kΩ stub (TP19/TP20) | ⚠ floating stub — see section |
| modem_rail | U4 | TPS61022 boost (modem rail) | **DONE ✓ (01:06 rebuild + 13:14 divider + 16:44 fixes)** — VIN/L2/SW/EN/MODE/GND/output cap ✓; FB divider R4 (330k) + R3 (47k) = 4.01 V ✓ (16:44: R3.2 re-routed to FB); C6 rewired VIN → -BATT ✓ (16:44) — only VBATT → U1 VBAT pins 55–57 remains |
| network_and_gps | U1 | SIM7670G-LNGV modem | to wire (GND stubs all terminated ✓, 39 wires) |
| network_and_gps | J1 | JAE SF72S006 SIM holder | to wire (GND symbol placed ✓) |
| accelerometer | U2 | LIS2DE12 (design says LIS2DW12 — OPEN) | to wire (0 wires; Vdd = 3.3V touch ✓, GND touch ✓) |
| sound | LS1 | `PCM_SparkFun-Electromechanical:PKLCS1212E4001-R1` (passive piezo) | **DONE ✓ (00:28)** — `LS1.P -> Buzzer+` label, `LS1.N -> GND #PWR071` direct |
| radio_layout | E1 | 2450AT18A0100001E chip antenna | to wire |
| radio_layout | L1, C1 | 4.7 nH + 1.2 pF BLE match | to wire |

## Status ledger (schematic state = 13:14 save, 20-08-2026)

One-glance DONE / NOT-DONE per sheet. The U5 pin-by-pin table below is the authoritative
state of the ESP32-C3. Subsheet connections (`clock`, `uart`) go through **sheet pins on
the sheet boxes inside brains** — pin = port on the box, wired to U5 on the brains
canvas; the matching `hierarchical_label` inside the subsheet is the other end.

### brains — U5 (ESP32-C3) pin-by-pin

| U5 pin | Name | State |
| ------ | ---- | ----- |
| 2/3 | VDD3P3 | **DONE ✓** 3.3V bus (pin 3 = hidden alias of pin 2, same tip) |
| 11/17/18/31/32 | VDD3P3_RTC/CPU/SPI/VDDA | **DONE ✓** 3.3V bus (pin 31 = hidden alias of pin 32, same tip) |
| 33 | GND | **DONE ✓** (pin-touch) |
| 29/30 | XTAL_N/P (40 MHz) | **DONE ✓ (01:06)** — clock sheet pin `XTAL_N`/`XTAL_P` @ (76.2, 80.01/82.55) → direct wires to pins 29/30 (tips (86.36, 80.01/82.55)) ✓ |
| 4/5 | XTAL_32K_N/P (32.768 kHz) | **DONE ✓ (01:06)** — clock sheet pins `XTAL_32K_N`/`XTAL_32K_P` @ (76.2, 77.47/74.93) → direct wires to pins 5/4 (tips (86.36, 77.47/74.93)) ✓ |
| 7 | CHIP_EN | **DONE ✓** R7 pull-up + SW2 + **`UART_CHIP_EN` sheet pin connected** (junction @ (153.67, 52.07): U5 pin 7 tip (132.08, 52.07) → (153.67, 52.07) → (153.67, 101.6) = UART sheet pin) |
| 9 | MTMS (= GPIO5) | **DONE ✓** SW3 status button (pin-touch) |
| 10 | MTDI (= GPIO4) | **DONE ✓** Motion_wake label → accelerometer (matches) |
| 15 | GPIO9 | **DONE ✓** R5 pull-up + SW1 — `I2C (SCL)` ✓ — **⚠ UART_GPIO9 sheet pin wired here (swap FIXED 12:14)** |
| 14 | GPIO8 | **DONE ✓** R6 pull-up + `I2C (SDA)` ✓ |
| 6 | GPIO2 | **DONE ✓** battery_voltage_sense label → modem sheet (matches) |
| 8 | GPIO3 | **DONE ✓** power-on_pulse label → modem sheet (matches) — **buzzer conflict RESOLVED (19-08): buzzer on GPIO7** |
| 13 | MTDO (= GPIO7) | **DONE ✓ (00:28)** — `Buzzer+` label ADDED on brains → sound sheet LS1 ✓ (see sound section) |
| 12 | MTCK (= GPIO6) | spare — kept free for hardware JTAG (MTCK = the clock line) |
| 27/28 | U0RXD/U0TXD | **DONE ✓** `UART (RXD)`/`UART (TXD)` labels → modem sheet (matches). J3 side connected via sheet pins ✓ — **swap FIXED (12:14, re-verified 16:5x): `UART_U0RXD` → U5 pin 27, `UART_GPIO9` → U5 pin 15, `UART_U0TXD` → pin 28** (see uart section) |
| 16 | GPIO10 | **DONE ✓** `LED_DIN` label → visual sheet (matches) |
| 25 | GPIO18 | **DONE ✓** modem_status label anchored on the pin tip (⚠ STATUS is 1.8 V logic, needs level-shift) |
| 26 | GPIO19 | **DONE ✓** modem_rail_switch label anchored on the pin tip |
| 1 | LNA_IN | untouched (antenna lives on radio_layout) |

### All sheets, done vs not

| Sheet | Done | Not done |
| ----- | ---- | -------- |
| brains | everything above + R5/R6/R7 + SW1/SW2/SW3 (63 wires, 13:14) | ✓ **complete — UART sheet-pin swap FIXED (12:14)** |
| clock | **DONE ✓ (01:06)** — Y1 32K CTS3 + Y2 40 MHz C3E + C13–C16 (22 pF), 25 wires, **connected to U5 via sheet pins** (pins 4/5/29/30) | nothing |
| uart | J3 + R14/R15 + TP8–TP13 + 4 labels (14 wires), **connected to U5 via sheet pins** | ✓ **complete — swap FIXED (12:14)** |
| power_input | **complete ✓** (32 wires, verified twice) | cosmetic: draw CC1→R1 and R2 pin 2→GND wires |
| visual | D1 WS2812B wired ✓ (8 wires) + C12 decap ✓ + `+BATT #PWR072` ✓ | — |
| network_and_gps | 39 GND stubs + GND symbol + 5 labels (UART TXD/RXD, battery_voltage_sense, power-on_pulse, modem_status) | all signal wiring U1/J1; STATUS level-shift |
| accelerometer | U2 placed, Vdd/GND touches, 3 labels (I2C (SCL)/(SDA), Motion_wake) | all wiring (0 wires) |
| modem_rail | **DONE ✓ (01:06 rebuild + 13:14 divider + 16:44 fixes)** — VIN ✓ (+BATT → L2 → SW), EN ✓ (modem_rail_switch), MODE ✓ (+BATT), GND ✓, output cap C7 ✓, FB divider R4 (330k) + R3 (47k) = 4.01 V ✓, C6 = VIN → -BATT decoupling ✓ | `#PWR054 VBATT -> U1 VBAT pins 55–57` (network_and_gps sheet) — the ~2 A TX burst lives there |
| li-ion_charging_handling1 (WORKING COPY — RESET 21:5x, refs = ORIGINAL's) | U6/U7/U8/BT1/SW4/Q1 wired (65 wires), power symbols + PIN± labels kept (21:5x) | **BMS → battery/charger wired ✓ (21:5x, agent): U7 pin 2 → cell − rail, U7 pin 5 → GND #PWR071, U6 VCC → BT1 (+)** — remaining: cell-side net (BT1 ↔ SW4/Q1/+BATT — Q1/SW4 0 wires), toggle jumper + R8/R9 (470k, placed unwired) |
| sound | **DONE ✓ (00:28)** — LS1.P → `Buzzer+` label, LS1.N → GND direct (6 wires) | — |
| radio_layout | E1/L1/C1 placed | **everything** (0 wires) |

### Open decisions (blocking wiring)

1. **GPIO3: buzzer (BZ1) vs power-on pulse (PWRKEY)** — **RESOLVED (19-08): buzzer → GPIO7 (MTDO), GPIO3 keeps PWRKEY** — wired both sides ✓ (00:28)
2. **D1 (WS2812) VDD rail** — **RESOLVED (00:30): `+BATT`** (sourced on li-ion1 U11 pin 5 BAT; visual has `+BATT #PWR072`)
3. **Charge-status on LED** — **RESOLVED (19-08): option B in effect** (LED1/LED2 CHRG/STDBY pair wired on li-ion; no ESP-input variant)
4. **U2: LIS2DE12 placed vs design LIS2DW12** — OPEN

## ASCII wiring references (for the wrong / unclear connections)

### 1. R6 — I2C SDA pull-up (brains sheet, DONE ✓, latest save)

```txt
NOW:  full pull-up chain verified — 3.3V symbol (#PWR058) @ (71.12, 50.8), R6 @ (77.47, 50.8)
      rot 90 (horizontal), pin 2 @ (73.66, 50.8) ← wire ← 3.3V; pin 1 @ (81.28, 50.8)
      → wire down → row y = 52.07 → I2C (SDA) label @ (71.12, 52.07) → GPIO8 @ (86.36, 52.07)

    #PWR058 3.3V ── R6 pin 2 ──[R6]── R6 pin 1 ──┬── I2C (SDA) label ──→ U2 pin 4
                                                 └── GPIO8 (U5 pin 14)
```

### 2. I2C (SCL) label — FIXED ✓ (brains sheet, latest save)

```txt
NOW (fixed):  the label moved to the GPIO9 side of R5 — label @ (44.45, 57.15),
              vertical stub (48.26, 57.15)→(48.26, 54.61), then the GPIO9 row
              (44.45→86.36, 54.61) → GPIO9 (U5 pin 15). R5: 3.3V #PWR056 @ (34.29, 54.61)
              → pin 1 @ (36.83, 54.61), pin 2 @ (44.45, 54.61) = row start.

    3.3V ── R5 10k ──┬── GPIO9
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

> **✅ DONE ✓ (verified 16:5x):** D1 VDD is on the `+BATT` rail and the rail IS sourced on
> li-ion1 — `visual: +BATT #PWR072 → C12 (100 nF) → D1.4 (VDD)`; `li-ion1: +BATT global
> label → U11 pin 5 (BAT)`. **No schematic update needed.** (The old "U6 pin 5 BAT" note
> below predates the 00:28 annotation — the charger is U11, and its BAT pin already sources +BATT.)

```txt
USER DECISION (23:52 save): D1 VDD is on the "+BATT" power symbol (now #PWR072 after the 01:56 annotation) —
the rail must be SOURCED on the li-ion sheet: place a "+BATT" power symbol at
U6 pin 5 BAT (downstream of SLIDE_SW). See visual section for the honest notes
(U6 BAT = same node as the cell +; Q2 P-FET turns +BATT into the protected rail;
3.3V from U3 = the regulated alternative, dimmer).

    li-ion:  BT1 (+) ── Q2 [optional] ── U6 pin 5 BAT ── [+BATT symbol]
    visual:  [+BATT symbol] ── C14 100nF ── D1 VDD
```

### 6. modem_rail sheet — the two wrong nets (biggest electrical risk)

> **✅ FIXED ✓ (01:06 rebuild + 16:44 regressions):** VIN now on the `+BATT` rail (was
> 3.3 V), output net is `VBATT` (was 5 V), C6 = VIN → -BATT decoupling, FB divider R4
> (330k) + R3 (47k) = 4.01 V. **modem_rail itself is done — no update needed.** The only
> wire left for this rail is `VBATT → U1 VBAT pins 55–57`, to be drawn on the
> network_and_gps sheet. (The ASCII below is the historical pre-rebuild state.)

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

### 7. R_UART_A/B — programmer backfeed protection — DONE ✓ (uart sheet)

```txt
The two 1 kΩ (R14/R15) sit IN SERIES on U0TXD (J3 pin 3) and U0RXD (J3 pin 4), on the
uart sheet, between the labels and J3 — everything downstream (J3, TPs, labels) is on
the protected side. 5 V TX → clamp current ~1.5 mA; TX↔TX conflict → ~3.3 mA max;
zero signal impact at 115200 baud. 3V3/GND/CHIP_EN/GPIO9 rows are direct wires (correct).

    uart sheet, y = 24.13:  UART_U0TXD label ── R14 1k ──┬── J3 pin 3 ─ TP9
    uart sheet, y = 26.67:  UART_U0RXD label ── R15 1k ──┬── J3 pin 4 ─ TP12
```

## Wiring — already done (verified by scan, 01:06 20-08-2026)

**brains (63 wires — re-laid out 19-08; U5 @ (109.22, 67.31)):**

- **3.3 V rail — all power pins:** `3.3V #PWR059 @ (99.06, 17.78) -> C8/C9/C10/C11 (pin 2)`
  and `-> U5 (pins 3 VDD3P3, 11 VDD3P3_RTC, 17 VDD3P3_CPU, 18 VDD_SPI, 32 VDDA)` ✓
  (pin 3 = hidden alias of pin 2, pin 31 = hidden alias of pin 32 — same tips, connected)
- **Decoupling:** `C8/C9/C10/C11 (pin 1) -> GND #PWR061` ✓
- `U5 (pin 33 GND) -> GND #PWR060` ✓ (pin-touch)
- **R_EN (R7) @ (172.72, 52.07):** `3.3V #PWR063 @ (180.34, 52.07) -> R7 (pin 2)` ✓ —
  `R7 (pin 1) -> U5 (pin 7 CHIP_EN)` ✓ (real wire, via the CHIP_EN junction @ (153.67, 52.07))
- **R_BOOT (R5) @ (40.64, 54.61):** `3.3V #PWR056 @ (34.29, 54.61) -> R5 (pin 1)` ✓ —
  `R5 (pin 2) -> U5 (pin 15 GPIO9)` ✓ — `I2C (SCL)` label on the GPIO9 node ✓
- **R_SDA (R6) @ (77.47, 50.8) rot 90:** `3.3V #PWR058 @ (71.12, 50.8) -> R6 (pin 2)`
  ✓ — `R6 (pin 1) -> I2C (SDA)` label + GPIO8 ✓
- **SW_RST (SW2) @ (158.75, 46.99):** `SW2 (pin 1) -> CHIP_EN` ✓, `SW2 (pin 2) -> GND
  #PWR062` ✓ (pin-touch)
- **SW_BOOT (SW1) @ (54.61, 52.07):** `SW1 (pin 2) -> GPIO9` ✓, `SW1 (pin 1) -> GND
  #PWR057` ✓ (pin-touch)
- **Status button (SW3) @ (191.77, 57.15):** `SW3 (pin 1) -> U5 (pin 9 MTMS)` ✓ (pin-touch
  @ (132.08, 57.15)), `SW3 (pin 2) -> GND #PWR064` ✓ (pin-touch)
- **Labels (11):** `power-on_pulse` (GPIO3), `battery_voltage_sense` (GPIO2),
  `Motion_wake` (pin 10 MTDI), `I2C (SCL)` (GPIO9), `I2C (SDA)` (GPIO8), `LED_DIN`
  (GPIO10), `modem_status` (GPIO18), `modem_rail_switch` (GPIO19), `UART (TXD)`/
  `UART (RXD)` (pins 28/27), **`Buzzer+` (pin 13 MTDO, ADDED 00:28 ✓)** — all match
  their counterpart sheets
- **Subsheet sheet pins (01:06):** clock box @ (60.96, 72.39) — 4 pins `XTAL_32K_P/N`
  @ (76.2, 74.93/77.47) + `XTAL_N/P` @ (76.2, 80.01/82.55), each wired to its U5 pin
  (4/5/29/30, tips (86.36, 74.93/77.47/80.01/82.55)) ✓ **clocks CONNECTED**; uart box @
  (137.16, 101.6) — 4 pins, **U0TXD + CHIP_EN correct, U0RXD↔GPIO9 SWAPPED** (see uart
  section)

**clock (NEW sheet, 00:28 — 25 wires, re-ref'd 01:06):**

- **Y1 (32.768 kHz, CTS3-32.768-12.5-20-R) @ (39.37, 29.21):** `Y1 (pin 1) -> C13 ->
  XTAL_32K_N` label, `Y1 (pin 2) -> C16 -> XTAL_32K_P` label ✓
- **Y2 (40 MHz, C3E40000121010X) @ (38.1, 53.34):** `Y2 (pin 1) -> C14 -> XTAL_P` label,
  `Y2 (pin 3) -> C15 -> XTAL_N` label, `Y2 (pin 4 case GND) -> GND #PWR077` ✓
- `C13/C16 (far ends) -> GND #PWR077`, `C14/C15 (far ends) -> GND #PWR077` ✓
- 4 hierarchical labels + 4 sheet pins on the brains box — **CONNECTED to U5 pins 4/5/29/30 ✓**

**power_input (32 wires, 19:37 save):**

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
| `U5 (pin 27 U0RXD) -> U1 (pin 9 TXD)` | modem AT UART — label "UART (RXD)" ✓ matches modem sheet |
| `U5 (pin 28 U0TXD) -> U1 (pin 10 RXD)` | modem AT UART — label "UART (TXD)" ✓ matches modem sheet |
| `U5 (pin 14 GPIO8) -> U2 (pin 4 SDA/SDI)` | I2C SDA — **DONE ✓ (pull-up R4 to 3.3V)**; label "I2C (SDA)" ✓ |
| `U5 (pin 15 GPIO9) -> U2 (pin 1 SCL/SPC)` | I2C SCL — **DONE ✓ (R3 pull-up doubles as SCL pull-up)** |
| `U5 (pin 10 MTDI = GPIO4) -> U2 (pin 12 INT1)` | motion wake — label "Motion_wake" ✓ matches accelerometer |
| `U5 (pin 6 GPIO2) -> U1 (pin 51 VBAT_ADC)` | battery voltage sense — label ✓ matches |
| `U5 (pin 9 MTMS = GPIO5) -> SW3 (pin 1)` | **DONE ✓** status button; `SW3 (pin 2) -> GND` — short = battery color on D1, long = BLE pairing (LED, not buzzer) |
| `U5 (pin 8 GPIO3) -> U1 (pin 1 PWRKEY)` | power-on pulse — label ✓ matches — **buzzer conflict RESOLVED (19-08): buzzer on GPIO7** |
| `U5 (pin 25 GPIO18) -> U1 (pin 66 STATUS)` | modem status — label anchored on the pin tip ✓; ⚠ STATUS is 1.8 V logic, needs level-shift |
| `U5 (pin 26 GPIO19) -> U4 (pin 5 EN)` | modem rail switch — label anchored on the pin tip ✓ |
| `U5 (pin 16 GPIO10) -> D1 (pin 3 DIN)` | status LED data — **DONE ✓** — `LED_DIN` on both sheets, both wired |
| `U5 (pin 13 MTDO = GPIO7) -> LS1 (pin 1 +)` | **DONE ✓ (00:28)** — `Buzzer+` label added on brains, matches sound ✓ |
| `U5 (pins 29/30 XTAL_P/N) -> clock sheet` | **DONE ✓ (01:06)** — sheet pin `XTAL_P` @ (76.2, 82.55) → U5 pin 30, `XTAL_N` @ (76.2, 80.01) → U5 pin 29 |
| `U5 (pins 4/5 XTAL_32K_P/N) -> clock sheet` | **DONE ✓ (01:06)** — sheet pin `XTAL_32K_P` @ (76.2, 74.93) → U5 pin 4, `XTAL_32K_N` @ (76.2, 77.47) → U5 pin 5 |
| `U5 (pin 28 U0TXD) -> J3 (pin 3)` + `U5 (pin 27 U0RXD) -> J3 (pin 4)` | **DONE ✓ (12:14 swap fix, re-verified 16:5x)** — `UART_U0TXD` @ (137.16, 111.76) → U5 pin 28 ✓; `UART_U0RXD` @ (137.16, 109.22) → U5 pin 27 ✓ (drop x=82.55, y 69.85→109.22); `UART_GPIO9` @ (137.16, 114.3) → U5 pin 15 ✓ (drop x=80.01, y 54.61→114.3) |
| `U5 (pin 7 CHIP_EN) -> J3 (pin 5)` | **DONE ✓ (01:06)** — `UART_CHIP_EN` @ (153.67, 101.6), vertical drop to junction @ (153.67, 52.07) ← U5 pin 7 + SW2.1 + R5.1 |
| `U5 (pin 15 GPIO9) -> J3 (pin 6)` | **DONE ✓ (12:14 swap fix, re-verified 16:5x)** — brains: GPIO9 row (y=54.61) → drop x=80.01 → `UART_GPIO9` sheet pin @ (137.16, 114.3); uart: label @ (31.75, 31.75) → TP13 → J3 pin 6 ✓ |

#### Programming interface (BOOT/RST switches + UART pads)

Why R_EN and R_BOOT exist (the two 10 kΩ you placed):

- **R_EN (R7)** — `CHIP_EN` is the chip's enable; the C3 stays on while it is high. The 10 kΩ to 3.3 V guarantees it is pulled up at power-on. The programmer (or SW_RST) pulls it low to reset.
- **R_BOOT (R5)** — `GPIO9` is a **strapping pin**: if it is LOW at power-up, the C3 enters **download mode** (UART bootloader) instead of running the firmware. The 10 kΩ to 3.3 V keeps it high = normal boot. To flash: hold GPIO9 low (SW_BOOT or the programmer pad) while resetting, then release.

Switches (still on brains — the **UART header moved out to the dedicated `uart` sheet**, see below):

| [PLACED] | Part | Wiring | Status |
| -------- | ---- | ------ | ------ |
| SW_RST (SW2) | momentary tact, 4.5×4.5 mm SMD (`Switch:SW_Push`) | `U5 (pin 7 CHIP_EN)` ↔ GND | ✓ done (18:10 version) |
| SW_BOOT (SW1) | momentary tact, same | `U5 (pin 15 GPIO9)` ↔ GND | ✓ done (18:10 version) |
| J3 | UART programming header — 1×6 pin, 2.54 mm THT (`Connector_PinHeader_2.54mm:PinHeader_1x06_P2.54mm_Vertical`), or 6 test points / castellated pads | on the `uart` sheet (16:09 annotation) @ (80.01, 25.4) — fully wired ✓ (14 wires + R14/R15 series + TP8–TP13) | ✓ connected via sheet pins — **UART swap FIXED ✓ (12:14)** |

> **ℹ Annotation pass (16:09 → 16:37, user) — UART TPs renumbered:** brains TP1–TP6 moved
> with J3 to the uart sheet and became **TP8–TP13** (3V3=TP8, U0TXD=TP9, CHIP_EN=TP10, GND=TP11,
> U0RXD=TP12, GPIO9=TP13). On li-ion, the probe row became **TP1–TP4** (BT1+ = TP1,
> BT1− = TP2, DW01A VCC = TP3, 8205A pin 5 = TP4), TEMP = TP5, R9 stub = TP6/TP7.
>
> **⚠ Programmer backfeed / reversed-wiring protection (18-08-2026) — DONE ✓ (uart sheet):**
>
> - **Hazard:** a backwards-plugged or 5 V-logic USB-serial adapter can (a) put 5 V TX onto
>   our U0RXD pad (C3 is NOT 5 V-tolerant), (b) TX↔TX fight both push-pull drivers, (c) feed
>   its VCC (3.3 V, or 5 V on cheap clones!) into the 3.3 V rail.
> - **[DONE] R_UART_A + R_UART_B = R14/R15 (1 kΩ 0603) in series on U0TXD (J3 pin 3) and
>   U0RXD (J3 pin 4)**, on the uart sheet near the J3 side (the user's diagonal layout;
>   the series pair was first annotated R10/R11, then renumbered to **R14/R15** in the
>   13:14 global pass; extra 1k_0603s the user first added on 3V3/GND/CHIP_EN/GPIO9 were **removed
>   16:37** — direct wires for those four rows are correct). 5 V TX → clamp current limited
>   to ~1.5 mA; TX↔TX conflict → ~3.3 mA max; zero signal impact at 115200 baud.
> - **Preferred flashing flow: power the board via USB-C (J2), not J3 pin 1** — then the
>   programmer only carries GND + 4 signals and has no VCC backfeed path at all. J3 pin 1
>   (3V3) stays as a bare-board jig option.
> - (Optional) BAT54 Schottky in series on J3 pin 1 if programmer-powering matters: blocks
>   reverse current, costs 0.3 V (C3 min VDD 3.0 V — tight but usable).
> - A fully-reversed 6-pin plug (VCC onto the GND pin) beats any of this — key the
>   connector / label the header, and never connect the programmer's 5 V VCC pin.

### uart (new sheet, 16:09 — subsheet of brains)

**J3** 1×6 header @ (80.01, 25.4) + **R14/R15** (1k_0603, the R_UART pair) + **TP8–TP13** +
`3.3V #PWR074` + `GND #PWR075`. **14 wires, 4 hierarchical labels — FULLY WIRED on this
sheet (16:37 save, verified 01:06)**. The user's diagonal layout:

| Row | Header pin | Series R | TP | Notes |
| --- | ---------- | -------- | -- | ----- |
| y = 19.05 | J3 pin 1 = 3V3 | – (R removed 16:37) | TP8 @ (52.07, 19.05) | `3.3V` symbol direct ✓ |
| y = 21.59 | J3 pin 2 = GND | – (R removed 16:37) | TP11 @ (66.04, 21.59) | `GND` symbol direct ✓ |
| y = 24.13 | J3 pin 3 = U0TXD | **R14** @ (36.83, 24.13) | TP9 @ (52.07, 24.13) | `UART_U0TXD` label @ (31.75, 24.13) → R14 pin 2 ✓ — series R between label and J3 ✓ |
| y = 26.67 | J3 pin 4 = U0RXD | **R15** @ (44.45, 26.67) | TP12 @ (66.04, 26.67) | `UART_U0RXD` label @ (31.75, 26.67) → R15 pin 2 ✓ |
| y = 29.21 | J3 pin 5 = CHIP_EN | – | TP10 @ (52.07, 29.21) | `UART_CHIP_EN` label @ (31.75, 29.21) → TP10 ✓ |
| y = 31.75 | J3 pin 6 = GPIO9 | – | TP13 @ (66.04, 31.75) | `UART_GPIO9` label @ (31.75, 31.75) → TP13 ✓ |

**✓ CONNECTED to U5 via sheet pins — SWAP BUG FIXED (12:14, verified).** The uart
sheet box on brains @ (137.16, 101.6) carries 4 sheet pins; **the user moved the pins on
the box AND re-routed the vertical drops** — all four now land on the right U5 pins:

| Sheet pin (uart box) | Wired to (brains canvas) | Verdict |
| -------------------- | ------------------------ | ------- |
| `UART_U0TXD` @ (137.16, 111.76) | U5 pin 28 (U0TXD) tip (86.36, 67.31) via vertical x = 81.28 (67.31 → 111.76) | ✓ correct |
| `UART_U0RXD` @ (137.16, 109.22) | U5 pin 27 (U0RXD) tip (86.36, 69.85) via vertical x = 82.55 (69.85 → 109.22) | ✓ correct (was SWAPPED) |
| `UART_GPIO9` @ (137.16, 114.3) | U5 pin 15 (GPIO9) tip (86.36, 54.61) via vertical x = 80.01 (54.61 → 114.3) | ✓ correct (was SWAPPED) |
| `UART_CHIP_EN` @ (153.67, 101.6) | vertical (153.67, 52.07 → 101.6) junction ← U5 pin 7 CHIP_EN (132.08, 52.07) + SW2.1 + R5.1 | ✓ correct |

Note: `U0TXD`/`U0RXD` nets on brains also carry the modem `UART (TXD)`/`UART
(RXD)` labels — that is correct (programming header shares the modem UART lines).

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

### li-ion_charging_handling1 (WORKING COPY — RESET 21:5x)

> **⚠ SHEET RESET by the owner (21:5x):** `li-ion_charging_handling1.kicad_sch` was
> replaced by a **fresh copy of the ORIGINAL sheet** — refs are back to the original's
> (U6 DW01A, U7 8205A, U8 TP4056, BT1, SW4, Q1, LED1/LED2, TP1–TP7, R8–R13,
> #PWR065–070) and ALL power symbols are present again (`+BATT #PWR067`, `3.3V #PWR068`,
> `5V #PWR069`, `-BATT #PWR065`, GND `#PWR066/#PWR070`, `PIN±(5V)` labels). The 20:06
> global-label conversion is SUPERSEDED — symbols stay as-is (owner instruction:
> "don't alter the symbols, and no new BAT+/BAT−/5V/3.3V power symbols").
>
> **21:5x: BMS → battery/charger WIRED ✓ (agent, re-applied on the reset sheet):**
> 1. `U6 (pin 5 VCC)` → `BT1 (+)`: wire `(45.72, 30.48) → (54.61, 30.48)` bridges the
>    TP1→TP3 gap on y = 30.48 — the VCC net (TP3/U6.5) now rides the cell + rail,
>    PRE-SW4 (protection armed while charging with the switch off).
> 2. `U7 (pin 2)` (cell-side drain) → **cell − rail**: wires `(44.45, 39.37) →
>    (44.45, 44.45) → (60.96, 44.45)` join BT1(−)/TP2 to the U6.6/U7.2 net.
> 3. `U7 (pin 5)` (system-side drain) → **GND**: new `GND #PWR071` @ (93.98, 20.32),
>    wire `(93.98, 26.67) → (93.98, 20.32)` (permanent FET path to board GND; TP4
>    stays on the net).
>
> ```txt
>                #PWR071 GND  (NEW symbol @ 93.98, 20.32)
>                    │  W2 (NEW): wire (93.98, 26.67) → (93.98, 20.32)
>                    │
>            (93.98, 26.67) ──── TP4 rail (existing) ──── U7 pin 5 (93.98, 38.1)
>                                    │
>    BT1 (+) ── rail ── TP1 (45.72, 30.48) ── W3 ── TP3 (54.61, 30.48) ── U6 pin 5 VCC
>       │         │       (NEW, 1 segment,   │           (62.23, 30.48)
>       │         │        y = 30.48 gap     │    TP3 stays as probe
>       │         │        bridge, 0 crosses)│
>       │         │                          │
>       │    BT1 (−) ── rail y = 39.37 ── 44.45 ──┐      │  DW01A now powered from
>       │         │                    │          │      │  cell +, PRE-SW4 (armed
>       │         │                    │          │      │  while charging)
>       │         │             W1 (NEW):         │      │
>       │         │       (44.45, 39.37) ── (44.45, 44.45) ── (60.96, 44.45)
>       │         │                                │      │
>    U6 pin 6 GND (62.23, 35.56) ── (60.96, 35.56) ─┘      │  cell − rail now:
>       │                                                   │  BT1(−) → TP2 → U6 GND → U7 pin 2
>       └── (60.96, 44.45) ── (82.55, 44.45) ── U7 pin 2 (cell-side drain @ 90.17, 38.1)
>
>    ⚠ still open (chain ①):  SW4 (140.97, 40.64) ── Q1 (160.02, 40.64) ── +BATT #PWR067 (168.91, 38.1)
>    ⚠ -BATT #PWR065 dangles on its own stub @ (135.89, 52.07) — left alone
>    U8 TP4056 charger: already wired ✓ (5V / 3.3V / GND symbols, BAT → +BATT #PWR067)
> ```
>
> **Route notes:** the FIRST attempt routed VCC straight to U6.5 (62.23, 30.48) and
> CROSSED the TP4 rail (x = 60.96 wall + y = 26.67 horizontal) — mid-wire crossings
> junction in KiCad → the sheet came out "mangled" and was reset. The TP1→TP3 gap
> bridge stops at (54.61, 30.48) and crosses nothing. All new segments verified:
> 0 mid-crossings, endpoint flood ✓ (`BT1+/TP1/TP3/U6.5`, `BT1−/TP2/U6.6/U7.2`,
> `U7.5/TP4/GND#PWR071`), parens balanced, diff = additions only. Backup:
> `bonus/tmp/li-ion_charging_handling1.kicad_sch.bak-reset-pre-wiring`.
> ⚠ KiCad open during the edit — reload li-ion1 before saving.
>
> **TL;DR (read this first):** four jobs live on this sheet, in this order — **① the cell
> chain** (BT1 → SW4 switch → Q1 reverse-protection FET → `+BATT`), **② the charger** (U8
> TP4056: 5V in → BAT out → `+BATT`, + the CHRG/STDBY LEDs), **③ the protection pair**
> (U6 DW01A + U7 8205A: the only path from cell − to GND), **④ the BMS-toggle** (solder
> pads + R8/R9 gate pull-ups). **② charger and ③ protection logic are DONE ✓ — ③ now
> fully wired into the battery/charger loop (21:5x): U7 pin 2 → cell − rail, U7 pin 5 →
> GND, U6 VCC → BT1 (+).** What remains is the **power chain ① (SW4/Q1 have 0 wires since
> the re-layout)** plus the toggle pads [ADD] and R8/R9 wiring, and a floating R13 stub.
> Details below; the wiring table marks each row `✓ done` or `⚠ open`.
> **⚠ Design rule (user, 20-08-2026): this sheet uses WIRE connections only — NO power
> symbols other than GND** (avoids an accidental feedback loop / spicy pillow). **DECIDED
> and APPLIED ✓ (20:06, on li-ion_charging_handling1 — see the banner above):** global
> labels on the CHARGING side only (the U11 block) — `+BATT` label @ U11 pin 5 BAT
> junction, `5V` label @ U11 pin 4 VCC, `3.3V` label @ U11 pin 8 CE; the BATTERY side
> stays pure wires: cell + chain BT2(+) → SW5 → Q2 → the `+BATT` label point; cell − rail
> BT2(−) → U10 (no label, no symbol). GND symbols (#PWR079/#PWR083) stay. The original
> sheet still has the old power symbols — the owner applies the same conversion there
> when satisfied with the duplicate.
> **⚠ Symbol pinouts on this sheet (read from the embedded blocks, 16:37 save) — refs
> renumbered by the 16:09 annotation pass (TP4056 U9→U11, 8205A U11→U10, DW01A U10→U9):**
>
> - **U9 DW01A** (`Battery_Management:DW01A` — **swapped from the old custom lib by the
>   user; 2 body styles now**: body 1 = the 5 active pins below, body 2 = TD alone):
>   1 = OD, 2 = CS, 3 = OC, **5 = VCC, 6 = GND**; **4 = TD is on body 2 — leave open ✓**
>   (same "leave open" call as before)
> - **U10 8205A** (custom SnapMagic-style lib `8205A`, 2 body styles; body 1 = all 6 pins):
>   anonymous pins — physical part (UMW datasheet, 19-08-2026): **1 = S1, 2 = D, 3 = S2,
>   4 = G2 (charge gate), 5 = D, 6 = G1 (discharge gate)**
> - **Q2** (`PCM_Transistor_MOSFET_AKL` Si2305CDS): **1 = G, 2 = S, 3 = D** (SOT-23)
> - **SW5** (`PCM_SparkFun-Switch` SPDT_Slide): **1 = A, 2 = 2, 3 = C**, plus MT1–MT4 mounting tabs
> - **U11 TP4056-42-ESOP8** (`Battery_Management`): 1 TEMP, 2 PROG, 3 GND, 4 VCC, 5 BAT,
>   6 STDBY, 7 CHRG, 8 CE, 9 EPAD ✓ matches

Parts on the sheet (13:14 save, positions re-scanned from the file): **BT2**
Battery_Cell @ (19.05, 35.56), **TP14**/**TP15**/**TP16**/**TP17** (TestPoint_1.25mm)
@ (45.72/54.61, 30.48/34.29) — the cell probe row, **U9** DW01A @
(72.39, 33.02), **U10** 8205A @ (91.44, 45.72) — protection pair,
**SW5** SPDT slide @ (140.97, 40.64), **Q2** Si2305CDS @ (160.02, 40.64) rot 90,
**U11** TP4056 @ (184.15, 52.07),
**LED3** red @ (153.67, 52.07) rot 180 + **LED4** green @ (153.67, 57.15) rot 180,
**R16/R17** **470 kΩ @ (111.76, 60.96)/(118.11, 52.07)** — the OD/OC pull-ups, placed
with 0 wires, **R10/R11** 1 kΩ LED resistors @ (166.37, 52.07/54.61),
**R12** 1.2 kΩ PROG resistor @ (195.58, 59.69) —
**⚠ Value field edited to `2.4k_0603`: 500 mA charge rate — the open question
(1 A vs 500 mA) looks RESOLVED; confirm the footprint is the 2.4 kΩ part**,
**R13** 10 kΩ @ (205.74, 59.69), **TP5** @ (195.58, 52.07), **TP6** @ (204.47, 52.07),
**TP7** @ (205.74, 63.5) rot 180 (R13 pin 2 touches TP7 pin 1 @ (205.74, 63.50) — coincident
tips = connected). **Power/label state (RESET 21:5x):** all original power symbols kept —
GND `#PWR066` @ (148.59, 57.15) + `#PWR070` @ (187.96, 67.31), **`#PWR071` @
(93.98, 20.32) — NEW (21:5x, U7 pin 5 → board GND, wire (93.98, 26.67) → (93.98, 20.32))**,
`+BATT #PWR067` @ (168.91, 38.1), `3.3V #PWR068` @ (172.72, 46.99), `5V #PWR069` @
(186.69, 33.02), `-BATT #PWR065` @ (135.89, 52.07) kept, `PIN+(5V)`/`PIN-(5V)`
hierarchical labels kept. **65 wires** on the sheet.

**Exact components missing on li-ion (to [ADD]):** **2× `TestPoint_1.25mm`** pads
for the DW01A-GND toggle (solder bridge; no 2-pad footprint — the 03:40 user spec). All
other parts are placed (U9/U10/U11/BT2/SW5/Q2/LED3/LED4/R16-R21/TP14–TP20) — **the OD/OC
pull-ups are on the sheet as R16/R17 (470 kΩ @ (111.76, 60.96)/(118.11, 52.07)), placed
but UNWIRED (0 wires)**.

> **ℹ Reading the cell nodes (18-08-2026):** `BT2` (the `Device:Battery_Cell` symbol on
> this sheet) **is** the battery — a 3.7 V nominal Li-ion pouch in the real device (3.0–4.2 V;
> cell wires soldered or a keyed 2-pin connector, e.g. Molex PicoBlade — a 2-pin JST-PH is
> NOT reliably keyed and can be plugged backwards). `BT2 (+)` / `BT2 (−)` are the symbol's
> actual pin names — treat them as *net names*, not connectors.
>
> **ℹ Hierarchy (user design):** li-ion_charging_handling **and** li-ion_charging_handling1
> are **subsheets of power_input** (sheets "Li-ion charging handling" / "Li-ion charging
> handling1" inside power_input.kicad_sch) — the battery and its
> charger live in the power path by design, so 5V and +BATT connect **directly** between the
> two sheets (global power nets, no labels) for stability.
>
> **ℹ Net semantics (user convention, 19-08-2026):** power symbols — `GND`, `3.3V`, `5V`,
> `+BATT`, `-BATT` — are **universal / bus-like**: anyone may read from AND write to them
> (they are global power nets, not directional). Hierarchical labels (`h`, e.g.
> `Motion_wake`, `power-on_pulse`) are the opposite: they route **through sheets** and the
> writing side lives on the sheet that owns the signal. Don't confuse the two kinds.
>
> **BMS toggle (user spec, reworked 03:30 19-08-2026) — DW01A-ENABLE scheme: no power-path
> switch, no population toggle:**
>
> - **U9 (DW01A) + U10 (8205A) are ALWAYS populated.** The 8205A FETs are the only path from
>   the cell − to board GND — permanent wires: pin 2 → the cell − rail, pin 5 → a `GND`
>   symbol. (The old `P_NEG` net and the bridged-bypass jumper are dropped.)
> - The toggle is a **2-pad switch in the DW01A's GND pin** (µA — nothing heats), built as
>   **two single `TestPoint_1.25mm` pads facing each other** (no 2-pad footprint at hand;
>   bridge them with solder for "closed"):
>   - **jumper OPEN (default — cell WITH internal BMS, typical 603050 pouch):** the DW01A
>     has no return path → dead → OD/OC float HIGH via the new **R16/R17 (470 kΩ)
>     pull-ups to +BATT** → both 8205A gates stay on → the FETs conduct like a wire.
>     No onboard protection — the battery's own BMS owns the cell.
>   - **jumper CLOSED (bare cell, no BMS):** the DW01A is powered (VCC → BT2 (+)) and
>     monitors the cell; OD/OC pull the gates for overcharge / overdischarge /
>     overcurrent. The 470 kΩ pull-ups are too weak to fight the open-drain trip.
> - The user's silkscreen text is now CORRECT as written: **"link only if the battery
>   does not have a BMS; otherwise leave open"** (open = default = with-BMS battery).
> - Safety: U9 VCC → BT2 (+) is PRE-SW5 so protection stays armed while charging with
>   the device off; jumper open = DW01A unpowered (GND floating → no current path).
>
> **Reverse-polarity protection — Q2 (Si2305CDS @ (160.02, 40.64) rot 90):** the U9/U10
> combo protects overcharge / overdischarge / overcurrent, **but NOT a reversed cell** —
> plugged backwards, the TP4056, DW01A and everything else get forward-biased body diodes
> and die. **Q2 = Si2305CDS** (≈55 mΩ): source = SW5 output, drain = +BATT, gate = BT2 (−);
> correct polarity: Vgs < 0 → on, **zero drop**; reversed: gate = +3.7 V vs source → off,
> body diode blocks. ⚠ **the on-chain is UNWIRED after the 03:21 re-layout** (stubs only —
> see the wiring table; was wired at 01:56 before the move). **Q2 (Si2301CDS) REMOVED ✓
> (03:21)** — a second P-FET on the battery rail had no role (Q2 covers reverse protection,
> U9/U10 cover the cell).

Charger & LEDs wired ✓ (01:18–01:40, re-verified 03:21/03:33/16:37/00:28/01:06/12:14/13:14): `5V -> U11 (pin 4 VCC)`,
`U11 (pin 8 CE) -> 3.3V`, `U11 (pin 5 BAT) -> +BATT #PWR080` (rail source @ (168.91, 38.1)),
`U11 (pin 7 CHRG) -> R18 -> LED3 red -> GND #PWR079`, `U11 (pin 6 STDBY) -> R19 -> LED4 green ->
GND`, `U11 (pin 2 PROG) -> R20 -> GND #PWR083`, `U11 (pin 3 GND + pin 9 EPAD) -> GND`,
`U11 (pin 1 TEMP) -> TP18`. ⚠ the cell-side net is OPEN: `BT2 (+)` runs
(19.05, 30.48) → (19.05, 26.67) → (44.45, 26.67) → (44.45, 30.48) → **TP14 only**;
`BT2 (−)` runs (19.05, 38.10) → (19.05, 39.37) → (44.45, 39.37) → (44.45, 34.29) →
**TP15 only**. On-chain stubs (03:33) were **REMOVED by the 16:xx re-layout — SW5 moved to
(140.97, 40.64), Q2 to (160.02, 40.64), and Q2/SW5 still have ZERO wires** (SW5 pin 1 A keeps
a 1-wire stub only). The on-chain must be re-routed in the new geometry (below).

> **Li-ion wiring diagram (13:14 geometry, mils):** cell + rail → probe row → on-switch →
> reverse-protection FET → +BATT. Power symbols are universal nets (no labels needed).

```txt
BT2 (+)──┬──TP14 ──────── (cell + probe)            BT2 (−)──┬──TP15 ──── (cell − probe)
         │                                                     │
         │                    ┌── SW5 (SPDT slide @ (140.97, 40.64))            │
         │                    │   pin 2 @ (135.89, 40.64)  ← BT2 +             │
         │                    │   pin 1 (A) @ (146.05, 38.10)                   │
         │                    └──────────┼─────────────────────────────────────┤
         │                               │                                     │
         │                    Q2 (Si2305CDS @ (160.02, 40.64) rot 90)           │
         │                      pin 2 (S) @ (165.10, 43.18) ← SW5 A            │
         │                      pin 3 (D) @ (154.94, 43.18) → +BATT            │
         │                      pin 1 (G) @ (161.29, 36.83) ← cell − (Q2 gate) │
         │                               │                                     │
         │                    +BATT #PWR080 @ (168.91, 38.1)                    │
         │                    ├── U11 pin 5 (BAT)   [charger out ✓]             │
         │                    └── R16/R17 (470k @ (111.76,60.96)/(118.11,52.07)) │
         │                        [placed, 0 wires] → U9 OD/OC                 │
         │                                                                     │
         │  U9 (DW01A @ (72.39, 33.02))          U10 (8205A @ (91.44, 45.72))    │
         │   VCC (5) → BT2 + PRE-SW5 [open]       pin 6 ← OD ✓   pin 4 ← OC ✓  │
         │   GND (6) ──┼── U10 pin 2 (cell-side)   pin 1+3 ← CS ✓               │
         │             │                          pin 5 → GND [open, was TP17]  │
         │             └── toggle: 2× TP_1.25mm pads [ADD] → cell −            │
         │                                                                     │
         └─────────────────────────────────────────────────────────────────────┘
```

| Wire | Notes |
| ---- | ----- |
| `BT2 (+) -> SW5 (pin 2)` | ⚠ open — BT2 + reaches TP14 only; needs `TP14 -> SW5 pin 2 @ (135.89, 40.64)` (or straight wire) |
| `SW5 (pin 1 A) -> Q2 (pin 2 S)`, `Q2 (pin 3 D) -> +BATT`, **`Q2 (pin 1 G) -> BT2 (−)`** | ⚠ open — Q2 has **no wires**; SW5 A @ (146.05, 38.10) [1-wire stub], Q2 S @ (165.10, 43.18), Q2 D @ (154.94, 43.18), Q2 G @ (161.29, 36.83), `+BATT` global label @ (168.91, 38.1), cell − rail = pure wires (BT2(−) → TP15). Route: SW5 A → Q2 S, Q2 D → the `+BATT` label point, Q2 G → the cell − rail |
| `U11 (pin 4 VCC) -> 5V` | ✓ done — 5V is global (USB-C from power_input, direct net: li-ion is a **subsheet of power_input** by design, so the battery/5V nets connect straight across) |
| `U11 (pin 5 BAT) -> +BATT` | ✓ done — via +BATT #PWR080 @ (168.91, 38.1) |
| `U11 (pin 8 CE) -> 3.3V` | ✓ done — #PWR081 @ (172.72, 46.99) |
| `U11 (pin 3 GND) -> GND`, `U11 (pin 9 EPAD) -> GND` | ✓ both done — #PWR083 @ (187.96, 67.31) |
| `U11 (pin 2 PROG) -> R20 -> GND` | ✓ done — **R20 Value = 2.4 kΩ = 500 mA charge rate** (the open question is settled: 500 mA / 0.5 C); confirm the 2.4 kΩ part is actually placed |
| `U11 (pin 7 CHRG) -> R18 (1 kΩ) -> LED3 (red) -> GND` | ✓ done — charging indicator (open-drain, active low) |
| `U11 (pin 6 STDBY) -> R19 (1 kΩ) -> LED4 (green) -> GND` | ✓ done — charged indicator |
| `U11 (pin 1 TEMP) -> TP18` | ✓ done — floating test point; NTC only if the cell has a TS pin |
| `U6 (pin 5 VCC) -> BT1 (+)` | ✓ **done (21:5x, re-applied on the RESET sheet)** — wire (45.72, 30.48) → (54.61, 30.48) bridges the TP1→TP3 gap on y = 30.48; the VCC net (TP3/U6.5) rides the **cell + node, PRE-SW4**: protection stays armed while charging with SW4 off. No mid-wire crossings (first attempt crossed the TP4 rail → sheet reset by owner) |
| `U6 (pin 6 GND) -> U7 (pin 2)` | ✓ done (03:21, re-verified 16:37) — U7 pin 2 now also on the **cell − rail** (21:5x); ⚠ must be **SPLIT per the DW01A-enable scheme**: U6 GND → toggle switch pad 1, pad 2 → cell −; U7 pin 2 stays on the cell − net (it is the cell-side drain) |
| `U9 (pin 2 CS) -> U10 (pin 1 S1 + pin 3 S2)` | ✓ done (03:21 rewire, re-verified 16:37) — the mid node (both 8205A sources) |
| `U9 (pin 1 OD) -> U10 (pin 6)` | ✓ **done (03:21 rewire, same as 02:30; re-verified 16:37)** — real discharge gate **G1** per the UMW datasheet (the old symbol called pin 6 "D") |
| `U9 (pin 3 OC) -> U10 (pin 4)` | ✓ **done (03:21 rewire; re-verified 16:37)** — real charge gate **G2** (datasheet) |
| `U9 (pin 4 TD)` | leave open ✓ (nothing wired — pin lives on DW01A body style 2, hidden) |
| `U7 (pin 2)` | ✓ **done (21:5x, re-applied on the RESET sheet)** — cell-side drain joined the **cell − rail**: wires (44.45, 39.37) → (44.45, 44.45) → (60.96, 44.45) tie BT1(−)/TP2 → U6 GND → U7 pin 2. ⚠ still to SPLIT per the DW01A-enable toggle (U6 GND → toggle pad 1, pad 2 → cell −) |
| `U7 (pin 5)` | ✓ **done (21:5x, re-applied on the RESET sheet)** — system-side drain now on **GND**: new `GND #PWR071` @ (93.98, 20.32) with wire (93.98, 26.67) → (93.98, 20.32) (permanent FET path to board GND; TP4 stays on the net) |
| `R16/R17 (470 kΩ)` | ⚠ **PLACED (12:14) but 0 wires** — OD node (U9 pin 1 / U10 pin 6) → `+BATT`, OC node (U9 pin 3 / U10 pin 4) → `+BATT` — keep both 8205A gates high when the DW01A is unpowered (switch open). R16 @ (111.76, 60.96) pins (111.76, 57.15/64.77); R17 @ (118.11, 52.07) pins (118.11, 48.26/55.88) |
| `Q2 (Si2301CDS)` | **REMOVED ✓ (03:21)** — no role (Q1 covers reverse protection) |
| `R21 (10 kΩ) + TP19/TP20` | ⚠ floating stub — pin 1 -> TP19 @ (204.47, 52.07), R21 @ (205.74, 59.69), pin 2 touches TP20 @ (205.74, 63.50) (coincident tips = connected); no net to anything. Battery-sense divider start, or delete? |
| `SW5 (pin 3 C) + MT1–MT4` | spare throw + mounting tabs — leave unwired ✓ |

#### LEDs + on/off button (decision 19-08-2026 — zero GPIO cost)

**Battery-status LEDs — free, from the TP4056 open-drain outputs** (the standard charger
circuit, active low) — **WIRED ✓ (01:18 save)**:

- **LED3 red = charging** ← `U11 (pin 7 CHRG)` via R18 (1 kΩ) — `LED3 K @ (149.86, 52.07) -> GND #PWR079` ✓
- **LED4 green = charged** ← `U11 (pin 6 STDBY)` via R19 (1 kΩ) — `LED4 K @ (149.86, 57.15) -> GND` ✓
- Both lit only while USB is plugged in — exactly when you're looking at them. 0 mA
  battery drain, 0 GPIOs.

**"Power" LED — deliberately NOT a rail LED.** An always-on 3.3V-rail LED burns ~2 mA
24/7 ≈ 48 mAh/day ≈ 5 % of the 1000 mAh budget. Instead:

- **D3 ← U1 (pin 52 NETLIGHT)** via 1 kΩ — the modem's built-in network-status blinker
  (off = modem off, 64 ms-on/800 ms-off = not registered, 64 ms-on/3 s-off = registered,
  64 ms-on/300 ms-off = data). Tells you the tracker is alive *and* connected. 0 firmware,
  0 GPIOs. (If size is a fight, this is the one to cut.)

**On/off button — a slide switch, PLACED as SW5 @ (140.97, 40.64), in the `BT2 (+)` path**,
between `U11 (pin 5 BAT)` and the rest of the board. Target wiring: `BT2 (+) -> SW5 pin 2`,
`SW5 pin 1 (A) -> Q2 (pin 2 S) -> Q2 (pin 3 D) -> +BATT` and `Q2 (pin 1 G) -> BT2 (−)`.
⚠ **the chain was fully wired at 01:56, unwired again at 03:21, and the 16:xx re-layout
moved SW5/Q2 to (140.97/160.02, 40.64) with Q2 back to ZERO wires** (only SW5 pin 1 A keeps
a 1-wire stub). New pin tips: SW5 A @ (146.05, 38.10), SW5 pin 2 @ (135.89, 40.64), SW5 C @
(146.05, 43.18), Q2 S @ (165.10, 43.18), Q2 D @ (154.94, 43.18), Q2 G @ (161.29, 36.83),
+BATT @ (168.91, 38.1), -BATT @ (135.89, 52.07) rot 180. Rationale:

- Every GPIO is allocated — no push-button input available without dropping a feature.
- CHIP_EN (SW2) can't be a soft power button: it *resets* the chip, it's not readable.
- A slide switch = true hard-off (µA-level leakage = none), works for storage/transport,
  and charging still works with it off (TP4056 BAT connects directly to the cell).
- Soft off stays as firmware: deep sleep + motion wake (U2 INT1), modem rail cut by
  GPIO19/EN, PWRKEY pulsed by GPIO3 on wake (firmware re-powers the modem — no auto-on cap).
- Part: tiny SMD slide switch (e.g. `Switch:SW_SPDT` 2.5 mm pitch, ~$0.20) — or a 2-pad
  solder jumper if the case can't fit a switch (open = off, storage only).

> **Status-LED logic (18-08-2026, user spec) — DECIDED ✓:** "display charge status on the
> LED while charging; when not charging, display only when the status button is pressed."
> **Option B is in effect: keep the Y2 crystal and the LED3/LED4 CHRG/STDBY LED pair** —
> charging state shown by the free LEDs (wired on li-ion ✓), D1 (WS2812) stays button-only.
> The ESP-input variants (A: drop Y2 → GPIO0/1 = CHRG/STDBY, or C: sacrifice the buzzer)
> are shelved — the buzzer now lives on GPIO7 (19-08) and A would duplicate the wired
> LED pair. Drop those two LEDs only if "charge complete" must wake the ESP.

### modem_rail

> **⚠ REBUILT (01:06 20-08-2026, 20 wires) + FB divider DONE (13:14) + power-symbol
> swap (14:46) + REGRESSIONS FIXED (16:44, 26 wires)** — U4 (TPS61022) @ (52.07, 48.26) + **L2 (1 µH)** + **C6 (input cap)** +
> **C7 (output cap)** + **R4 (330 kΩ) + R3 (47 kΩ)
> (FB divider, SnapMagic RC0603FR-07330KL / RC0603FR-0747KL)** + `#PWR049 +BATT`,
> `#PWR050 +BATT`, `#PWR052 GND`, `#PWR053 -BATT`, `#PWR054 VBATT`, `#PWR055 -BATT`,
> `#PWR051 -BATT` +
> `modem_rail_switch` label (brains side matches). **The `5V` symbol is GONE — output net
> is `VBATT`** ✓. **19:32: global #PWR annotation** (this sheet: +BATT #PWR049/#PWR050,
> -BATT #PWR051, GND #PWR052, -BATT #PWR053, VBATT #PWR054, -BATT #PWR055; symbols moved,
> nets re-verified ✓).
> **16:44 fixes: `#PWR050 +BATT` moved (46.99,19.05) → (43.18,38.1) onto the VIN node;
> `#PWR051 -BATT` added @ (46.99,20.32) for C6.1; R3.2 (85.09,62.23) re-routed up the
> y = 53.34 row to the FB node (68.58,50.8).**
> Current net state (16:44 scan):
>
> | Net | Members | Verdict |
> | --- | ------- | ------- |
> | +BATT | #PWR049 → U4 pin 6 (MODE); #PWR050 → VIN node | ✓ correct |
> | VIN | #PWR050 +BATT, C6.2, L2.1 → U4 pin 7 (VIN) | ✓ +BATT feeds the boost directly; C6.2 decouples VIN |
> | SW | L2.2 → U4 pin 2 (SW) | ✓ boost inductor VIN→SW |
> | GND/-BATT | U4 pin 1 (GND), #PWR052 GND, #PWR053 -BATT, R3.1, #PWR051 -BATT → C6.1 | ✓ (cell − = GND) |
> | FB | **R4.1 + R3.2 + U4 pin 4 (FB)** | ✓ divider complete: VOUT → R4 (330k) → FB → R3 (47k) → GND = 4.01 V |
> | VOUT | U4 pin 3 (VOUT), C7.1, #PWR054 VBATT, R4.2 | ✓ (R3.2 no longer on VOUT) |
> | EN | U4 pin 5 (EN) → `modem_rail_switch` label | ✓ |
>
> **⚠ Proposed (user's li-ion wire-only rule, 20-08-2026):** the `-BATT` symbols here
> (#PWR051/#PWR053/#PWR055) lose their li-ion anchor once li-ion's cell − becomes pure
> wires. They are already wired onto the GND node (this table's `GND/-BATT` row) — cell −
> = GND through U7 — so **rename them to `GND`** (electrically identical, keeps the boost
> ground on the protected side). Proposed on paper only; the owner applies it in KiCad.
> **16:44 FIXED — FB divider regression (14:46):** R3 @ (77.47, 62.23) pin 2
> (85.09, 62.23) was hanging on the VOUT trunk at x = 91.44 (47 kΩ as a VOUT→GND load,
> no bottom leg to FB). **Fixed: R3.2 → (85.09,53.34) → (68.58,53.34) → (68.58,50.8)
> joins the FB row next to R4.1.** Divider now VOUT → R4 (330k) → FB → R3 (47k) → GND.
>
> **16:44 FIXED — C6 series-cap issue (13:14):** was `+BATT #PWR050 → C6.1; C6.2 → VIN`
> (DC blocked). **Fixed: `+BATT #PWR050` moved onto the VIN node (43.18,38.1), C6.1 →
> `-BATT #PWR051` @ (46.99,20.32), C6.2 stays on VIN** — parallel input decoupling, per
> the TPS61022 datasheet.

Remaining wiring:

| Wire | Notes |
| ---- | ----- |
| `#PWR054 VBATT -> U1 VBAT pins 55–57` | **[ADD]** the 4.0 V output feeds the modem (network_and_gps sheet) + 22 µF + 100 nF — the ~2 A TX burst lives here |

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
| `U2 (pin 12 INT1) -> U5 (pin 10 MTDI = GPIO4)` | wake-on-motion — RTC GPIO, deep-sleep EXT1 wake |
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
| `LS1 (pin P +) -> U5 (pin 13 GPIO7/MTDO)` | **DONE ✓ (00:28)** — via the `Buzzer+` labels on both sheets, direct GPIO drive, **no transistor** — LEDC PWM square wave (2–4 kHz, 50% duty) plays real melodies (pitch + rhythm); bit-banged GPIO toggling works too (same wiring) |
| `LS1 (pin N −) -> GND` | **DONE ✓ (00:28)** — direct `GND #PWR071` symbol on the sound sheet |
| (optional) 100 nF across LS1 | only for the active-buzzer variant; not needed for the piezo |

Buzzer decision (19-08-2026):

- **Swap BZ1 for a passive piezo transducer: Murata PKLCS1212E4001-R1 (12×12×2 mm, ~75 dB,
  2 g) or PKLCS0909E4001-R1 (9×9×1.9 mm — the tiny pick).** The placed SparkFun 9 mm symbol
  is an *active* buzzer (fixed ~2.7 kHz, 20–30 mA → needs a driver transistor, rhythm-only
  "melodies"). The passive piezo: ~2 mA, direct GPIO, and the ESP32-C3's LEDC plays
  pitch-accurate melodies (find-me pattern, low-battery tone, SOS).
- Symbol: keep the 2-pin SparkFun symbol as a placeholder or use `Device:Buzzer`; footprint
  = the Murata PKLCS pad layout.

> **✅ DONE (00:28 20-08-2026):** the sound sheet's LS1 (PKLCS1212E4001-R1 piezo) is
> **fully wired** — `LS1.P -> Buzzer+` label, `LS1.N -> GND #PWR071` (direct power symbol;
> the old `Buzzer-` label was dropped). **brains got its matching `Buzzer+` label on
> U5 pin 13 (MTDO = GPIO7)** ✓ — the net is CLOSED, no labels left to add.
>
> **GPIO conflict RESOLVED (19-08-2026): the buzzer moves OFF GPIO3 → U5 pin 13
> (GPIO7/MTDO).** GPIO3 keeps the `power-on_pulse` label (modem PWRKEY) it already has on
> brains ✓ — no PWRKEY cap needed. GPIO6/7 (MTCK/MTDO) are the two **JTAG-only pins**: no
> strapping role, no RTC need → ideal for a PWM buzzer. MTCK (pin 12) stays spare in case
> hardware JTAG is ever wanted; MTDO (pin 13) takes BZ1/LS1.

- Wiring: `U5 (pin 13 GPIO7/MTDO) -> BZ1 (+)`, `BZ1 (−) -> GND`. GPIO7 is a JTAG-only pin
  (MTDO) with no strapping role — no conflict with anything (no PWRKEY cap needed either).
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
| R_UART_A/B | 1 kΩ ×2 0402 | in series on J3/TP side of U0TXD + U0RXD — programmer backfeed/reversed-plug protection, **DECIDED — DONE ✓ (R14/R15 on the uart sheet)** |
| Q2 | P-MOSFET SOT-23 (SI2301/SI2305 class, ~$0.05) | reverse-polarity protection (zero drop), **DECIDED — PLACED as Q1 = Si2305CDS @ (160.02, 40.64) rot 90; ⚠ unwired again after the 16:xx re-layout (Q1 = 0 wires — redo the chain)** |
| R_EN | 10 kΩ 0402 | U5 pin 7 -> 3.3V — **DONE ✓ (R7)** |
| R_SDA | 4.7 kΩ 0402 | U5 pin 14 (GPIO8/SDA) -> 3.3V — I2C pull-up + straps GPIO8 high, **DECIDED — DONE ✓ (R6 @ (77.47, 50.8), wired to 3.3V #PWR058)** |
| R_BOOT | 10 kΩ 0402 | U5 pin 15 -> 3.3V — **DONE ✓ (R5)** (doubles as I2C SCL pull-up) |
| SW_RST | tact switch SMD 4.5×4.5 mm | U5 pin 7 (CHIP_EN) ↔ GND — **DONE ✓ (SW2)** |
| SW_BOOT | tact switch SMD 4.5×4.5 mm | U5 pin 15 (GPIO9) ↔ GND — **DONE ✓ (SW1)** |
| J3 | 1×6 pin 2.54 mm THT header (or 6 test pads) | 3V3, GND, U0TXD (pin 28), U0RXD (pin 27), CHIP_EN (pin 7), GPIO9 (pin 15) — **DONE ✓ (on the uart sheet, 14 wires; connected to U5 via sheet pins)** — **UART swap FIXED ✓ (12:14)** |
| R12 (PROG) | 2.4 kΩ 0603 (was 1.2 kΩ) | U8 pin 2 -> GND — **PLACED ✓, WIRED ✓ — R12 Value edited to `2.4k_0603` = 500 mA charge rate (0.5 C)** |
| FB divider R_FB_HI/R_FB_LO | 330 kΩ / 47 kΩ 0603 | U4 VOUT -> FB -> GND (4.0 V rail) — **DONE ✓ (13:14): R4 (330k, RC0603FR-07330KL) VOUT→FB, R3 (47k, RC0603FR-0747KL) FB→GND = 4.01 V**; **16:44: R3.2 re-routed to the FB row (was on VOUT since 14:46) ✓ — divider fully restored** |
| L2 | 1 µH 0603 | U4 pin 2 SW <-> BT1 (+) pin — **PLACED ✓ (01:06 rebuild)** |
| U7 | 8205A SOT-23-6 (**always populated** — the only cell − → GND path) | U6 gates, see li-ion sheet — **PLACED @ (91.44, 45.72); real part: 1=S1, 2=D, 3=S2, 4=G2, 5=D, 6=G1; OD→6 / OC→4 / CS→1+3 WIRED ✓ (03:21 rewire, re-verified 00:28); drains: pin 2 → cell − ✓ (21:5x on li-ion1, via (44.45,39.37)→(44.45,44.45)→(60.96,44.45)), pin 5 → GND ✓ (21:5x, #PWR071 @ (93.98,20.32))** |
| Q1 | Si2305CDS P-MOSFET SOT-23 | reverse-polarity protection — ⚠ **0 wires since the 16:xx re-layout** (S = SW4 A, D = +BATT, G = BT1 (−) — redo) |
| Q2 | Si2301CDS P-MOSFET SOT-23 | **REMOVED ✓ (03:21)** — no role (Q1 covers reverse protection) |
| R8/R9 (gate hold-ups) | 470 kΩ ×2 0402 | U6 pin 1 OD → +BATT, U6 pin 3 OC → +BATT — keep the 8205A gates high while the DW01A is unpowered (jumper open); open-drain trip still wins (470 kΩ vs the DW01A's pull-down), **DECIDED — PLACED as R8/R9 @ (111.76, 60.96)/(118.11, 52.07), ⚠ 0 wires** |
| BZ1 | passive piezo, Murata PKLCS1212E4001-R1 (12×12×2 mm) or PKLCS0909E4001-R1 (9×9×1.9 mm) | U5 pin 13 (GPIO7/MTDO) -> (+) -> (−) -> GND — **DONE ✓ as LS1 (00:28): P → Buzzer+ label, N → GND direct; brains `Buzzer+` on GPIO7 ✓ — net closed** |
| D1, D2 | red + green LED 0402/0603 | U9 pin 7 (CHRG) / pin 6 (STDBY) — charging / charged, **DECIDED — DONE ✓ (LED3/LED4 on li-ion1)** |
| D3 | LED 0402/0603 | U1 pin 52 (NETLIGHT) — network-status blinker, **DECIDED** |
| R_LED1/2/3 | 1 kΩ ×3 0402 | in series with D1/D2/D3 |
| D1 | WS2812B-2020 status LED (2×2×0.75 mm, 1-wire addressable) | U5 pin 16 (GPIO10) -> DI; VDD -> `+BATT #PWR072`; VSS -> GND; DOUT -> no-connect; + C12 100 nF at LED — info/status colors, **DECIDED, PLACED ✓ (visual sheet, 8 wires)** |
| SW4 (on/off slide switch) | tiny SMD slide switch SPDT (`SPDT_Slide_SMD_6.7x2.6mm` — pins 1 A / 2 2 / 3 C) | in BT1 (+), between U8 pin 5 (BAT) and the load — hard on/off, **PLACED ✓ @ (140.97, 40.64); ⚠ unwired (the BT1+ → SW4 pin 2 → A → Q1 S chain must be re-routed)** |
| SW3 (status button) | tact switch SMD 4.5×4.5 mm (same as SW1/SW2) | U5 pin 9 (MTMS = GPIO5) ↔ GND — status button (short = battery color on D1, long = BLE pairing), **DONE ✓** |
| Jumper | **two single `TestPoint_1.25mm` pads facing each other** (no 2-pad footprint at hand — bridge with solder = closed) | **U6 (pin 6 GND) <-> cell −** — the DW01A-enable toggle (µA), **DECIDED [ADD — NOT PLACED yet]**; default OPEN (with-BMS battery) |
| U.FL + antenna | U.FL + LTE flex | U1 pin 60 |
| GNSS patch | 25×25 mm passive | U1 pin 90 |
| Optional | NTC, 1PPS, ESD USBLC6-2SC6, extra status LED on GPIO18/STATUS, **D_VBUS Schottky (SS14/SS24, [ADD]) on J2 VBUS — reverse/mis-wired supply protection (TP4056 has none built in)**, TVS + PPTC on VBUS if hand-probed a lot | per rows above |

## GPIO allocation (PROPOSED — all free GPIOs of the C3 QFN-32)

Pin references use the **symbol pin names** (`MTMS`, `MTDI`, `U0TXD`, …); the GPIO number
follows in parentheses where the name is a function name.

| U5 pin (name) | GPIO | Assigned to |
| ------------- | ---- | ----------- |
| 28 (U0TXD) | 21 | U1 pin 9 (TXD) — modem AT UART ✓ |
| 27 (U0RXD) | 20 | U1 pin 10 (RXD) — modem AT UART ✓ |
| 14 (GPIO8) | 8 | U2 pin 4 (SDA) — strapping pin, ignored at boot when GPIO9 is high; R4 pull-up ✓ |
| 15 (GPIO9) | 9 | U2 pin 1 (SCL) — strapping pin, R3 pull-up ✓ — **UART_GPIO9 sheet pin ✓ (swap FIXED 12:14, re-verified 16:5x)** |
| **9 (MTMS)** | **5** | **SW3 status button (RTC GPIO → EXT1 deep-sleep wake) — DONE ✓** |
| **10 (MTDI)** | **4** | **U2 pin 12 (INT1, motion wake) — DONE ✓** (MTDI is RTC, EXT1-capable) |
| 6 (GPIO2) | 2 | U1 pin 51 (VBAT_ADC) — strapping pin, OK as ADC input ✓ |
| 8 (GPIO3) | 3 | U1 pin 1 (PWRKEY) — power-on pulse — **conflict RESOLVED: buzzer on GPIO7** ✓ |
| 25 (GPIO18) | 18 | U1 pin 66 (STATUS) — ⚠ SIM7670G STATUS is **1.8 V logic**; 3.3 V GPIO won't read it high → level-shift (2N7002 + 10 kΩ pull-up) or drive an LED from STATUS instead |
| 26 (GPIO19) | 19 | U4 pin 5 (EN, modem rail) ✓ |
| 16 (GPIO10) | 10 | **D1 WS2812B-2020 info LED (1-wire color status) — PLACED ✓ on visual sheet** — not RTC-capable (fine, LED doesn't need wake) |
| **13 (MTDO)** | **7** | **LS1 (buzzer, LEDC melody) — DONE ✓ (00:28): `Buzzer+` label on both sheets, LS1.N → GND direct** — JTAG-only pin, no strapping role, not RTC — fine for PWM |
| 12 (MTCK) | 6 | spare — kept free so hardware JTAG stays possible (MTCK = the JTAG clock line) |

> **Status button SW3 — DECIDED (19-08-2026), DONE ✓: U5 pin 9 MTMS (= GPIO5), button → GND,
> internal pull-up.** RTC-capable → EXT1 wake from deep sleep. **The button drives the
> LED (D1), not the buzzer** — click taxonomy (firmware, all on existing hardware):
>
> - **short click** → battery status as a **color on D1** (red <25 % / yellow ~50 % /
>   green ~2/3 / cyan full), measured via U1 pin 51 (VBAT_ADC) → U5 pin 6 (GPIO2).
> - **long click (≥3 s)** → BLE pairing mode (Find My / Find My Device registration window,
>   D1 flashes the pairing pattern).
> - The piezo (GPIO7) is NOT part of the button logic — it keeps its own alerts
>   (alarm/melodies triggered by motion events or firmware events only).
> - **D1 (WS2812B-2020, visual sheet) on U5 pin 16 (GPIO10) shows the colors** — battery
>   level color, pairing flash, modem-heartbeat blink. 1-wire protocol driven by the C3's
>   RMT peripheral; GPIO10 is not a strapping pin, so a boot-time low on the data line is
>   harmless. `LED_DIN` label on both sheets ✓.
>
> **Visual sheet — D1 WS2812B-2020 status LED @ (43.18, 27.94):** all connections wired
> (8 wires), C10 (100 nF) decap across VDD/VSS ✓:
>
> - **`+BATT #PWR072 @ (19.05, 20.32)`** — wire — `C12 pin 1` — wire — `D1 VDD` ✓
> - `GND #PWR073 @ (19.05, 39.37)` — wire — `C12 pin 2` — wire — `D1 VSS` ✓
> - `LED_DIN` label — wire — `D1 DIN` ✓
> - `D1 DOUT` — no-connect flag ✓ (single LED, no daisy chain)
>
> **⚠ Motion-wake correction (19-08-2026):** the original INT1 → U5 pin 16 (GPIO10) plan
> could NOT wake the C3 from deep sleep — only RTC GPIOs (GPIO0–5) do EXT1 wake, and
> GPIO10 is not one. INT1 now goes to **U5 pin 10 MTDI (= GPIO4, RTC)**, so EXT1 wakes on
> motion AND button. MTMS/MTDI (GPIO4/5) are the JTAG pins — using them as GPIO disables
> hardware JTAG debugging; flashing stays UART-based via J3, which was the plan anyway.

## Progress assessment (20-08-2026, 13:14 save — re-scanned from the files)

**Overall: ~85 % of the schematic is electrically final.** brains at **63 wires** (crystals
moved OFF to the new `clock` sheet, 25 wires); li-ion at **61 wires** — charger fully
wired (VCC, CE, BAT→+BATT, PROG→R12, GND+EPAD), CHRG/STDBY LED pair wired (R10/R11),
TEMP→TP5, and the **protection pair wired**: OD→pin 6 (G1), OC→pin 4 (G2), CS→pins 1+3,
U6 GND→U7 pin 2 (re-verified 13:14). **Q2 REMOVED ✓. Buzzer CLOSED ✓ (00:28): `Buzzer+`
label on brains (U5 pin 13 MTDO) + sound LS1 fully wired (P → label, N → GND direct).**
**UART sheet-pin swap FIXED ✓ (12:14) — all 4 pins land on the right U5 pins. FB divider
DONE ✓ (13:14) + RESTORED ✓ (16:44): R4 (330k) + R3 (47k) = 4.01 V on modem_rail
(R3.2 re-routed to the FB node after the 14:46 regression). C6 FIXED ✓ (16:44): input
cap now VIN → -BATT parallel decoupling — +BATT #PWR050 feeds the VIN node directly.** Remaining on
li-ion: the cell-side net (BT1+/− only reach TP1/TP2), the SW4→Q1→+BATT on-chain redo
(Q1 still 0 wires), U6 VCC → BT1 (+), the drains (pin 2 → cell −, pin 5 → GND), the
DW01A-enable jumper + R8/R9 pull-ups (placed, 0 wires). The clocks were connected via
sheet pins on the brains sheet box (01:06, verified) ✓.

**Done & verified (20-08-2026, 00:28 save):**

- **Full-project annotation (00:28, user)** — power symbols renumbered globally (clean
  per-sheet ranges under the **00:28 numbering** — superseded by the 19:32 pass, current
  ranges in the save-state block above); brains decaps **C10–C13 → C6–C9**; **crystals +
  load caps moved to the NEW `clock` sheet** as Y1/Y2 + C11–C14; visual C14 → **C10**.
- **`Buzzer+` label ADDED on brains (00:28)** — U5 pin 13 (MTDO = GPIO7) — matches the
  sound sheet ✓; sound sheet re-wired: `LS1.N -> GND #PWR068` direct (the `Buzzer-`
  label dropped) → **the buzzer net is CLOSED**.
- **Clock sheet fully wired (00:28, 25 wires)** — Y1 (40 MHz) + C12/C13 + `XTAL_P`/
  `XTAL_N` labels, Y2 (32.768 kHz) + C11/C14 + `XTAL_32K_P`/`XTAL_32K_N` labels, both
  case-GND pins + cap far ends on the clock GND symbols. ⚠ brains labels missing (see
  above) — the clocks are not connected to U5 yet.
- **uart sheet unchanged (16:37)** — 14 wires, 4 labels, R10/R11 series pair ✓; ⚠ brains
  labels still missing.
- **modem_rail GUTTED (00:28)** — bare U4 (TPS61022) + 3.3V (VIN, wrong) + GND +
  5V (VOUT, wrong) + 1 wire (00:28 numbering); L2/FB/caps/EN/MODE removed — the
  two show-stopper issues survive (VIN on LDO rail, VOUT labeled 5V).
- **Sound sheet DONE ✓ (00:28)** — LS1.P → `Buzzer+`, LS1.N → GND (6 wires).
- **Protection pair re-verified (00:28)** — OD→U7 pin 6 ✓, OC→U7 pin 4 ✓, CS→U7 pins 1+3
  ✓, U6 GND↔U7 pin 2 ✓, U6 VCC→TP3 ✓ (⚠ unpowered), U7 pin 5→TP4 ✓ (⚠ no GND yet),
  TD free ✓; charger rows unchanged (U8 = TP4056).
- **Brains re-verified (00:28, 63 wires)** — 3.3V rail on pins 3/11/17/18/32 (pins 2/31
  are hidden aliases at the same tips) ✓, decaps C6–C9 ✓, R3/R4/R5 + SW1/SW2/SW3 ✓, 11
  labels all matched ✓ (incl. the new `Buzzer+`).

**Fix before continuing (4 flags):**

1. **clock ↔ brains: 4 XTAL labels missing on brains (00:28)** — add `XTAL_P`/`XTAL_N`/
   `XTAL_32K_P`/`XTAL_32K_N` on the U5 pin 29/30 and 4/5 stubs
2. **uart ↔ brains: 4 UART labels missing on brains** — `UART_U0TXD`/`UART_U0RXD` on
   pins 28/27, `UART_CHIP_EN` on CHIP_EN, `UART_GPIO9` on GPIO9 (see uart section)
3. modem_rail (gutted): U4 VIN on 3.3V + VOUT labeled 5V (kills the modem at 4.3 V max) —
   rebuild L2/FB/caps/EN/MODE per the section
4. Charge-status logic: **option B (LED3/LED4 pair) is WIRED on li-ion1 ✓** — the
   ESP-input variant (A: drop Y2 → GPIO0/1 = CHRG/STDBY) would duplicate it; shelved.

**Li-ion next steps (open wiring, 00:28 state):** the on-chain is still reset (Q1 has
ZERO wires). Cell net first — `BT1 (+) -> SW4 pin 2` (from TP1; SW4 pin 2 tip @
(135.89, 40.64)), `SW4 pin 1 (A) -> Q1 pin 2 (S)` (A tip @ (146.05, 38.10), S tip @
(165.10, 43.18)), `Q1 pin 3 (D)` → the `+BATT #PWR067` symbol (D tip @ (154.94, 43.18),
symbol @ (168.91, 38.1)), `Q1 pin 1 (G) -> BT1 (−)` (G tip
@ (161.29, 36.83), via the `-BATT #PWR065` symbol @ (135.89, 52.07) rot 180); then the
BMS-enable scheme: **U6 VCC → BT1 (+)** (pre-SW4, keeps protection armed while charging
with the device off), **split U6 GND off U7 pin 2** → toggle switch pad 1 + pad 2 → cell −,
**U7 pin 5 → GND** symbol (was TP4), **U7 pin 2 → cell − rail** (via the `-BATT` power
symbol @ (135.89, 52.07)), **R12/R13 470 kΩ** on OD/OC → +BATT, **toggle switch = two
`TestPoint_1.25mm` pads facing each other** (solder bridge = closed). ⚠ delete the two
redundant `PIN+(5V)`/`PIN-(5V)` labels. Open questions kept:
R9/TP6/TP7 stub (battery-sense divider or delete?), R8 1.2 kΩ = 1 A charge rate (2.4 kΩ =
500 mA if preferred).

**Biggest remaining blocks:** **brains-side labels for the uart sheet (4)** and **for the
clock sheet (4)** (both sheets are fully wired on their side but disconnected from U5),
modem_rail rebuild (L2 + FB divider + caps + EN/MODE), U1 & J1 wiring on network_and_gps
(39 GND stubs done), accelerometer wiring (0 wires), li-ion BMS-toggle + SW4→Q1→+BATT
chain.

**Risk watch:** modem_rail is the only electrical show-stopper if it ships as-is
(VIN on the 500 mA LDO rail + VOUT at 5 V > 4.3 V max); everything else is connectivity
hygiene.

**Historical log (19-08-2026, 16:09 → 16:37 — old refs kept as written):**

- **Re-layout pass (16:xx, user, part of the 16:37 save)** — the charger/LED/PROG block
  moved right on li-ion: U8 → (184.15, 52.07), LED1/LED2 → (153.67, 52.07/57.15) rot 180,
  R6/R7 → (166.37, 52.07/54.61), R8/R9 → (195.58/205.74, 59.69), TP5/TP6/TP7 →
  (195.58/204.47, 52.07) + (205.74, 63.5), SW4 → (140.97, 40.64), Q1 → (160.02, 40.64)
  rot 90, power symbols per the 16:09 renumber; **on-chain stubs removed — Q1 back to
  ZERO wires** (61 wires on the sheet; re-scanned).
- **uart sheet fully wired (16:37 scan)** — 4 hierarchical labels added: `UART_U0TXD` @
  (31.75, 24.13) → R14 pin 2, `UART_U0RXD` @ (31.75, 26.67) → R15 pin 2, `UART_CHIP_EN` @
  (31.75, 29.21) → TP10 → J3 pin 5, `UART_GPIO9` @ (31.75, 31.75) → TP13 → J3 pin 6;
  series pair renumbered **R12/R13 → R10/R11 → R14/R15** (user annotations). ⚠ brains side still
  needs the matching labels (not connected yet). Tooling: `net_trace.py` now reports
  label names in net members (label regex missed `(shape …)` lines).
- **Full-project annotation pass (16:09, user)** — li-ion: TP4056 **U6→U8**, 8205A
  **U8→U7**, DW01A **U7→U6** (and the DW01A swapped to the standard `Battery_Management`
  lib — body 1 = OD/CS/OC/VCC/GND, TD on body 2, leave open ✓); probe row TP10–13→
  **TP1–TP4** (BT1+ = TP1, BT1− = TP2, U6 VCC = TP3, U7 pin 5 = TP4), TEMP TP7→TP5,
  R9 stub TP8/TP9→TP6/TP7; 1k_0603s renumbered (final: li-ion LED pair stays R6/R7, uart
  series pair = R10/R11); crystal caps rotated C6↔C7↔C8 (all 22 pF, connectivity
  unchanged); **UART section MOVED to a new `uart` sheet** (J3 + 6 TPs + R10/R11 series
  on U0TXD/U0RXD — the extra 1k_0603s the user first placed on 3V3/GND/CHIP_EN/GPIO9
  were removed at 16:37; direct wires there are correct)
- **Protection pair re-verified (16:37 scan, post-annotation)** — OD→U7 pin 6 ✓, OC→U7
  pin 4 ✓, CS→U7 pins 1+3 ✓, U6 GND↔U7 pin 2 ✓, U6 VCC→TP3 ✓, U7 pin 5→TP4 ✓, TD free ✓;
  charger rows unchanged (U8 = TP4056)
- **Tooling fix (16:45)** — `bonus/tmp/sch_full.py` + `net_trace.py` now resolve embedded
  symbol defs like KiCad does (body-style order within the symbol), not by the suffix
  digits — the old keying wrongly resolved U6/U7 to their 1-pin alternate bodies
- **Li-ion RE-LAYOUT + rewire (03:21 save, 54 wires)** — everything moved: U7 → (72.39,
  33.02), U8 → (91.44, 45.72), SW4 → (133.35, 33.02), Q1 → (152.40, 33.02) rot 90, U6 →
  (176.53, 44.45), LED1/LED2 → (146.05, 44.45/49.53), -BATT → (128.27, 44.45) rot 270,
  +BATT → (161.29, 30.48), new TP12/TP13 next to TP10/TP11. **Q2 REMOVED ✓.** Protection
  pair re-wired at the new spot: **OD→pin 6 ✓, OC→pin 4 ✓, CS→pins 1+3 ✓, U7 GND→U8 pin 2 ✓**
  (cell-side drain node), **U8 pin 5→TP13** (system-side drain — needs GND), **U7 VCC→TP11**
  (still unpowered). Cell net open: BT1 + → TP10 only, BT1 − → TP12 only (SW4/Q1 unwired —
  the 01:56 chain must be redone). **BMS toggle reworked 03:30 (doc): DW01A-enable scheme** —
  always-populated U7/U8, jumper moves into the U7 GND pin (µA), R12/R13 470 kΩ pull-ups on
  OD/OC → +BATT, pin 5 → GND permanent; no power-path switch, nothing heats.
- **Cell stubs + on-chain stubs (03:33 save, 57 wires)** — BT1 + routed (19.05, 30.48) →
  (19.05, 26.67) → (44.45, 26.67) → (44.45, 30.48) → TP10; BT1 − routed (19.05, 38.10) →
  (19.05, 39.37) → (44.45, 39.37) → (44.45, 34.29) → TP12. On-chain stubs: `SW4 A` →
  (147.32, 30.48) (above Q1 D @ (147.32, 35.56)), `+BATT` → (157.48, 30.48) (above Q1 S @
  (157.48, 35.56)), `-BATT` → (151.13, 36.83) (the cell − rail). **Toggle switch spec: two
  `TestPoint_1.25mm` pads facing each other** (no 2-pad footprint — user note, 03:40).
- **LED data line CLOSED ✓** — brains `LED_DIN` wired to GPIO10, visual `LED_DIN` wired
  to D1 DIN — one net, two sheets
- **+BATT rail SOURCED ✓** — `+BATT` global label on li-ion1 wired to U11 pin 5 BAT; visual's
  `+BATT` consumes it (now #PWR072)
- **Annotation (01:56)** — **Q1 (pin 1 G) → BT1 (−) WIRED ✓ (gate chain complete, undone
  03:21 — redo)**; NEW `-BATT` symbol added on the BT1 (−) rail; li-ion power symbols
  renumbered; visual: +BATT, GND renumbered
- **Annotation (01:18)** — visual: +BATT/GND renumbered; li-ion power symbols renumbered;
  resistors renumbered: R6/R7 = 1 kΩ (LED pair), R8 = 1.2 kΩ (PROG), R9 = 10 kΩ (stub);
  Q1 placed as Si2305CDS, Q2 as Si2301CDS
- **8205A pins 2+5 SPLIT (02:47–02:48, user)** — pin 5 moved, visible; pin 2 nudged.
  Drains are now two separate nets — the bare-cell BMS wiring is expressible.
  OD→pin 6, OC→pin 4, CS→pins 1+3 unchanged ✓
- **8205A symbol RESTORED + OD fixed (02:28–02:30)** — user re-downloaded the SnapMagic
  8205A from SnapEDA (fresh zip @ 02:27; the other model had corrupted the pin names):
  restored symbol is anonymous (no pin names), pin 5 hidden at pin 2's spot (drains
  merged → one net; ⚠ blocks the bare-cell mode, fine for bridged default). **U7 OD moved
  from U8 pin 2 → pin 6 (real G1) — DONE ✓ (02:30 save)**; OC → pin 4 (real G2) and
  CS → pins 1+3 unchanged ✓
- **8205A pinout verified against UMW datasheets (02:08)** — real UMW 8205A =
  1=S1, 2=D, 3=S2, 4=G2, 5=D, 6=G1; the pre-restore symbol labels were wrong on pins
  2/4/5/6
- **li-ion charger wired ✓ (01:18 save, 28 wires)** — 5V→VCC, CE→3.3V, BAT→+BATT,
  PROG→R8 (1.2 kΩ, = 1 A — swap for 2.4 kΩ if 500 mA preferred)→GND, GND pin 3 + EPAD→GND,
  CHRG→R6→LED1 (red)→GND, STDBY→R7→LED2 (green)→GND, TEMP→TP7
- **Sound sheet wired ✓ (23:02)** — LS1 (PKLCS1212E4001-R1 piezo) on `Buzzer+`/`Buzzer-`
  labels (6 wires) — brains labels added 00:28, now CLOSED ✓
- **R4 pull-up COMPLETE ✓** — `3.3V` → `R4 pin 2` → `pin 1` → `I2C (SDA)` label + GPIO8
- **I2C (SCL) FIXED ✓** — label @ (44.45, 57.15), stub down to the GPIO9 row → GPIO9
- **Cross-sheet label names ALL MATCH ✓** — `UART (TXD)`/`UART (RXD)`, `power-on_pulse`,
  `battery_voltage_sense`, `Motion_wake`, `modem_status`, `modem_rail_switch`
- **GPIO18/19 connected ✓ (label anchors on the pin tips)**
- J3 (6/6) + TP8–TP13 ✓ (uart) | SW1/SW2/SW3 ✓ | power_input ✓ (32 wires) | visual: D1 ✓

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
| Solder jumper | KiCad | two `TestPoint:TestPoint_1.25mm` pads facing each other (the BMS toggle — NOT the bridged `SolderJumper-2` part; ships OPEN = with-BMS default) |
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
