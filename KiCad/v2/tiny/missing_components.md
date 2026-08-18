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
-- LAST Modified: 18:14:43 18-08-2026
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

| Net | Meaning | Source |
| --- | ------- | ------ |
| `5V` | USB-C input rail | J2 VBUS |
| `3.3V` | logic rail | U3 V_OUT (ME6211) |
| `CELL_POS` | battery positive | BT1 (+), TP4056 BAT, U4 VIN, DW01A VCC |
| `CELL_NEG` | battery negative (raw) | BT1 (−) |
| `P_NEG` | protected negative | Q1 D2 / jumper pad 1 |
| `VBAT_MODEM` | ~4.0 V modem rail | U4 V_OUT (TPS61022) |
| `GND` | ground | everywhere |

## Present on the sheets

| Sheet | Ref | Part | Wiring |
| ----- | --- | ---- | ------ |
| brains | U5 | ESP32-C3 (QFN-32) | partial ✓ |
| brains | Y1 | C3E-40.000-12-1010-X (40 MHz) | done ✓ |
| brains | C2, C3 | 22 pF crystal load caps | done ✓ |
| brains | C4–C7 | 100 nF decoupling caps | done ✓ (pin 1 → GND, pin 2 → 3.3V) |
| brains | R4 | 10 kΩ CHIP_EN pull-up (= R_EN) | done ✓ (3.3V → CHIP_EN, pins touch) |
| brains | R3 | 10 kΩ GPIO9 pull-up (= R_BOOT) | done ✓ (GPIO9 → 3.3V) |
| brains | Y2 + C8, C9 | 32.768 kHz crystal (CTS3-32.768) + 2× 22 pF | done ✓ (full network wired to U5 pins 4/5) |
| brains | SW1 | tact switch — RST (CHIP_EN) | placed — ⚠ wired wrong (see brains section) |
| brains | SW2 | tact switch — BOOT (GPIO9) | done ✓ (pin 1 → GND, pin 2 → GPIO9) |
| power_input | J2 | USB-C charge-only | done ✓ |
| power_input | U3 | ME6211C33M5 (3.3 V LDO) | done ✓ |
| power_input | R1, R2 | 5.1 kΩ CC pull-downs | done ✓ |
| li-ion_charging_handling | U6 | TP4056-42-ESOP8 charger | to wire |
| li-ion_charging_handling | U7 | DW01A protection | to wire |
| li-ion_charging_handling | BT1 | `Device:Battery_Cell` (1000 mAh) | to wire |
| modem_rail | U4 | TPS61022 boost (modem rail) | to wire — ⚠ VIN on 3.3V net + VOUT labeled 5V (both wrong, see section) |
| network_and_gps | U1 | SIM7670G-LNGV modem | to wire (GND stubs all terminated ✓) |
| network_and_gps | J1 | JAE SF72S006 SIM holder | to wire (GND symbol placed ✓) |
| accelerometer | U2 | LIS2DE12 (design says LIS2DW12 — OPEN) | to wire (Vdd = 3.3V touch ✓, GND touch ✓) |
| sound | BZ1 | `PCM_SparkFun-Electromechanical:Buzzer_9mm_SMD` | [PLACED] — needs driver + GPIO (see sound section) |
| radio_layout | E1 | 2450AT18A0100001E chip antenna | to wire |
| radio_layout | L1, C1 | 4.7 nH + 1.2 pF BLE match | to wire |

## Wiring — already done (verified by scan, 19-08-2026)

**brains (54 wires, re-laid out 19-08-2026 — U5 @ (109.22, 67.31), Y1 @ (22.86, 73.66)):**

- `U5 (pin 30 XTAL_P) -> Y1 (pin 1)` via C2 ✓ (bus y = 82.55)
- `U5 (pin 29 XTAL_N) -> Y1 (pin 3)` via C3 ✓ (routed along y = 80.01)
- `C2 (pin 1) <-> C3 (pin 2)` — coincident midpoint @ (30.48, 95.25) ✓
- `Midpoint -> Y1 (pin 2 case GND)` -> `GND` ✓ (case pin + GND symbol @ (30.48, 97.79))
- `Y1 (pin 4 case GND)` — floats, OK
- `U5 (pin 33 GND) -> GND` ✓
- **3.3 V rail — all five power pins on the y = 40.64 bus:** `3.3V -> U5 (pin 2 VDD3P3)`, `3.3V -> U5 (pin 11 VDD3P3_RTC)`, `3.3V -> U5 (pin 17 VDD3P3_CPU)`, `3.3V -> U5 (pin 18 VDD_SPI)`, `3.3V -> U5 (pin 31 VDDA)` ✓
- **Decoupling fixed — caps now parallel, not series:** `C4/C5/C6/C7 (pin 1) -> GND` (chain @ y = 22.86/35.56 from the GND symbol @ (138.43, 35.56)), `C4/C5/C6/C7 (pin 2) -> 3.3V` (the 40.64 bus) ✓
- **32 kHz RTC network — new, fully wired ✓:** `Y2 (pin 1) -> C8 -> U5 (pin 5 XTAL_32K_N)`, `Y2 (pin 2) -> C9 -> U5 (pin 4 XTAL_32K_P)` (buses y = 86.36 / y = 74.93), `C8/C9 (far ends) -> GND` @ (49.53, 78.74)
- **R_EN (R4) @ (172.72, 52.07):** `3.3V @ (180.34, 52.07) -> R4 (pin 1)` ✓ — `R4 (pin 2) -> U5 (pin 7 CHIP_EN)` ✓ (real wire, row @ (153.67, 52.07))
- **R_BOOT (R3) @ (40.64, 54.61):** `3.3V @ (34.29, 54.61) -> R3 (pin 1)` ✓ — `R3 (pin 2) -> U5 (pin 15 GPIO9)` ✓ (row @ (59.69, 54.61))
- **SW_RST (SW2) @ (158.75, 48.26) — done ✓:** `SW2 (pin 1) -> CHIP_EN` (row @ (153.67, 52.07)), `SW2 (pin 2) -> GND` (touch @ (163.83, 48.26))
- **SW_BOOT (SW1) @ (54.61, 52.07) — done ✓:** `SW1 (pin 2) -> GPIO9` (junction @ (59.69, 54.61)), `SW1 (pin 1) -> GND` (touch @ (49.53, 52.07))

> **ℹ Note: R3/R4 swapped roles vs. this doc's earlier revision** — R3 is now the GPIO9
> pull-up (R_BOOT), R4 the CHIP_EN pull-up (R_EN).

> **✓ 18:10 version — switches fixed, verified by scan:**
>
> - **SW1 (BOOT) @ (54.61, 52.07):** `SW1 (pin 2) -> GPIO9` (row junction @ (59.69, 54.61),
>   between R3 pin 2 and U5 pin 15) ✓; `SW1 (pin 1) -> GND` (touch @ (49.53, 52.07)) ✓.
>   Hold SW1 + tap SW2 → download mode. No rail short: the 10 kΩ stays on the 3.3V side.
> - **SW2 (RST) @ (158.75, 48.26):** `SW2 (pin 1) -> CHIP_EN` (row @ (153.67, 52.07), from
>   U5 pin 7 @ (132.08, 52.07)) ✓; `SW2 (pin 2) -> GND` (touch @ (163.83, 48.26)) ✓.
>   Press = CHIP_EN low directly (R4 pulls it back up). Correct reset switch.
> - **R_BOOT (R3) @ (40.64, 54.61):** `3.3V @ (34.29, 54.61) -> R3 (pin 1)` ✓;
>   `R3 (pin 2) -> GPIO9` ✓.
> - **R_EN (R4) @ (172.72, 52.07):** `3.3V @ (180.34, 52.07) -> R4 (pin 1)` ✓;
>   `R4 (pin 2) -> CHIP_EN` ✓ (real wire).
>
> Cosmetic leftovers: dangling wire ends @ (29.21, 54.61) (left of the R3 pull-up symbol)
> and @ (43.18, 54.61) may still exist — harmless, can be trimmed. Switch↔GND links are
> pin-touches — check the junction dots render.

> **ℹ Coincident pins = connected in KiCad** (a junction dot renders there), but they are
> fragile: R3/R4 and the 3.3V/GND symbols rely on pins touching without wires. If a pin is
> nudged, the connection silently disappears — check the junction dots render.

**power_input (18 wires):**

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
| `U5 (pin 27 U0RXD) -> U1 (pin 9 TXD)` | modem AT UART |
| `U5 (pin 28 U0TXD) -> U1 (pin 10 RXD)` | modem AT UART |
| `U5 (pin 14 GPIO8) -> U2 (pin 4 SDA/SDI)` | I2C SDA (PROPOSED) |
| `U5 (pin 15 GPIO9) -> U2 (pin 1 SCL/SPC)` | I2C SCL (PROPOSED) — shares the R_BOOT pull-up node |
| `U5 (pin 16 GPIO10) -> U2 (pin 12 INT1)` | motion wake (PROPOSED) |
| `U5 (pin 6 GPIO2) -> U1 (pin 51 VBAT_ADC)` | battery voltage sense (PROPOSED) |
| `U5 (pin 8 GPIO3) -> U1 (pin 1 PWRKEY)` | power-on pulse (PROPOSED) |
| `U5 (pin 25 GPIO18) -> U1 (pin 66 STATUS)` | modem status / LED (PROPOSED) |
| `U5 (pin 26 GPIO19) -> U4 (pin 5 EN)` | modem rail switch (PROPOSED) |

#### Programming interface (BOOT/RST switches + UART pads)

Why R_EN and R_BOOT exist (the two 10 kΩ you placed):

- **R_EN (R4)** — `CHIP_EN` is the chip's enable; the C3 stays on while it is high. The 10 kΩ to 3.3 V guarantees it is pulled up at power-on. The programmer (or SW_RST) pulls it low to reset.
- **R_BOOT (R3)** — `GPIO9` is a **strapping pin**: if it is LOW at power-up, the C3 enters **download mode** (UART bootloader) instead of running the firmware. The 10 kΩ to 3.3 V keeps it high = normal boot. To flash: hold GPIO9 low (SW_BOOT or the programmer pad) while resetting, then release.

Switches already on the sheets:

| [PLACED] | Part | Wiring | Status |
| -------- | ---- | ------ | ------ |
| SW_RST (SW2) | momentary tact, 4.5×4.5 mm SMD (`Switch:SW_Push`) | `U5 (pin 7 CHIP_EN)` ↔ GND | ✓ done (18:10 version) |
| SW_BOOT (SW1) | momentary tact, same | `U5 (pin 15 GPIO9)` ↔ GND | ✓ done (18:10 version) |
| J3 | **UART programming header — 1×6 pin, 2.54 mm THT** (`Connector_PinHeader_2.54mm:PinHeader_1x06_P2.54mm_Vertical`), or 6 test points / castellated pads | see table below |

| Pad # | Net | Wiring |
| ----- | --- | ------ |
| 1 | 3V3 | from the programmer (strapping a 2-pin bridge powers the bare board) |
| 2 | GND | |
| 3 | U0TXD | `J3 (pin 3) -> U5 (pin 28)` — C3 TX → programmer RX |
| 4 | U0RXD | `J3 (pin 4) -> U5 (pin 27)` — C3 RX ← programmer TX |
| 5 | CHIP_EN | `J3 (pin 5) -> U5 (pin 7)` — programmer pulses low→high to reset into bootloader |
| 6 | GPIO9 | `J3 (pin 6) -> U5 (pin 15)` — programmer drives LOW at reset → download mode (R_BOOT pull-up is fine; programmer overrides) |

The THT header doubles as a test jig for the dev variant; on tiny it can be
castellated/solder pads instead if the case forbids the height.

### power_input

| Wire | Notes |
| ---- | ----- |
| `U3 (pin 3 CE) -> 5V` | enable the LDO — tie to VIN (internal pull-up exists, explicit tie is safer) |
| `U3 (pin 4 NC)` | leave unconnected |
| `C_IN -> 5V` and `C_IN -> GND` | [ADD] **1 µF input cap at U3 VIN** — ME6211 datasheet minimum, place near pin 1 |
| `C_OUT -> 3.3V` and `C_OUT -> GND` | [ADD] **10 µF (+ 100 nF) at U3 VOUT** — LDO stability + rail bulk |
| `C_VBUS -> 5V` and `C_VBUS -> GND` | [ADD, recommended] 10 µF near J2 VBUS (cable inductance) |

Review verdict 19-08-2026: the wired part is **electrically correct** — no series-cap
mistake, no shorts: J2 CC1/CC2 → R1/R2 → GND ✓, VBUS×2 → 5V → U3 VIN ✓, U3 VOUT →
3.3V ✓, J2 GND×2 + SHIELD → U3 VSS → GND ✓. Only the caps + CE tie are missing.

### li-ion_charging_handling

| Wire | Notes |
| ---- | ----- |
| `BT1 (+) -> CELL_POS` | cell positive |
| `BT1 (−) -> CELL_NEG` | cell negative |
| `U6 (pin 4 VCC) -> 5V` | charger input from USB-C |
| `U6 (pin 5 BAT) -> CELL_POS` | charge output to the cell |
| `U6 (pin 3 GND) -> GND`, `U6 (pin 9 EPAD) -> GND` | |
| `U6 (pin 2 PROG) -> R_PROG (pin 1)` | [ADD] 1.2–2.4 kΩ; `R_PROG (pin 2) -> GND` (2.4 kΩ = 500 mA = 0.5 C, recommended; 1.2 kΩ = 1 A max) |
| `U6 (pin 8 CE) -> 3.3V` | charge enable |
| `U6 (pin 1 TEMP) -> NTC 10 kΩ -> GND` | [ADD, optional — only if the cell has a TS pin] |
| `U6 (pin 7 CHRG)` / `U6 (pin 6 STDBY)` | [ADD, optional] charge LEDs + 1 kΩ |
| `U7 (pin 5 VCC) -> CELL_POS` | DW01A supply |
| `U7 (pin 6 GND) -> CELL_NEG` | |
| `U7 (pin 2 CS) -> Q1 mid node` | [ADD Q1] see below |
| `U7 (pin 3 OC) -> Q1 (pin 4 G1)` | charge gate |
| `U7 (pin 1 OD) -> Q1 (pin 2 G2)` | discharge gate |
| `U7 (pin 4 TD)` | leave open |
| `Q1 (pin 1 S1) + (pin 3 S2) -> U7 (pin 2 CS)` | [ADD] 8205A, SOT-23-6, unpopulated by default |
| `Q1 (pin 6/5 D1/D2)` | D1 -> CELL_NEG, D2 -> P_NEG |
| `Jumper (pad 1) -> P_NEG`, `Jumper (pad 2) -> GND` | [ADD] 2-pad solder jumper, **bridged by default** = protection bypassed; open only for a bare cell, then populate U7 + Q1 |

### modem_rail

> **⚠ Two issues to fix before wiring (scan 19-08-2026):**
>
> 1. **U4 (pin 7 VIN) @ (45.72, 22.86) is touching a `3.3V` symbol** — the boost is fed from
>    the ME6211 LDO rail (500 mA max), which cannot feed ~2 A TX bursts, and cascading
>    LDO→boost wrecks efficiency. VIN must go to `CELL_POS`.
> 2. **U4 (pin 3 VOUT) @ (60.96, 30.48) is wired to a `5V` symbol @ (62.23, 30.48)** —
>    SIM7670G `VBAT` max is **4.3 V**; 5 V would kill the modem. VOUT must be the
>    ~4.0 V `VBAT_MODEM` net (via the FB divider below), so relabel that symbol `VBAT_MODEM`
>    and rewire FB, or the modem rail is wrong topologically AND electrically.

| Wire | Notes |
| ---- | ----- |
| `U4 (pin 7 VIN) -> CELL_POS` | ⚠ **currently on 3.3V — move the symbol**; + 22 µF decap [ADD] |
| `U4 (pin 2 SW) -> L2 (pin 1)` | [ADD] 1 µH; `L2 (pin 2) -> CELL_POS` |
| `U4 (pin 3 VOUT) -> VBAT_MODEM` | ⚠ **currently labeled 5V — relabel to VBAT_MODEM (4.0 V)**; + 22 µF decap [ADD] |
| `U4 (pin 4 FB) -> R3 (pin 1)` | [ADD] ~700 kΩ; `R3 (pin 2) -> U4 (pin 3 VOUT)`; `U4 (pin 4 FB) -> R4 (pin 1)` [ADD] 100 kΩ; `R4 (pin 2) -> GND` → VOUT = 0.5 × (1 + 700/100) = **4.0 V** |
| `U4 (pin 5 EN) -> U5 (pin 26 GPIO19)` | C3 turns the modem rail off in deep sleep |
| `U4 (pin 6 MODE) -> CELL_POS` | PFM for light-load idle (PROPOSED; GND = forced PWM) |
| `U4 (pin 1 GND) -> GND` | |

### network_and_gps

| Wire | Notes |
| ---- | ----- |
| `U1 (pin 55/56/57 VBAT) -> VBAT_MODEM` | + 22 µF + 100 nF [ADD] — the ~2 A TX burst lives here |
| `U1 (pin 9 TXD) -> U5 (pin 27 U0RXD)` | AT UART |
| `U1 (pin 10 RXD) -> U5 (pin 28 U0TXD)` | AT UART |
| `U1 (pin 1 PWRKEY) -> U5 (pin 8 GPIO3)` | pulse low ≥ 500 ms to power on/off |
| `U1 (pin 66 STATUS) -> U5 (pin 25 GPIO18)` | modem status sense |
| `U1 (pin 51 VBAT_ADC) -> U5 (pin 6 GPIO2)` | battery sense (alternate: resistor divider from CELL_POS) |
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
| `U2 (pin 4 SDA/SDI) -> U5 (pin 14 GPIO8)` | I2C data |
| `U2 (pin 1 SCL/SPC) -> U5 (pin 15 GPIO9)` | I2C clock |
| `U2 (pin 3 SA0/SDO) -> GND` | I2C address 0x19 (or 3.3V = 0x18) |
| `U2 (pin 2 CS) -> 3.3V` | I2C mode (CS inactive) |
| `U2 (pin 12 INT1) -> U5 (pin 16 GPIO10)` | wake-on-motion |
| `U2 (pin 11 INT2)` | leave NC |
| `U2 (pin 5 RES)` | leave NC (internal pull-up) |

### radio_layout

| Wire | Notes |
| ---- | ----- |
| `U5 (pin 1 LNA_IN) -> L1 (pin 1)` | cross-sheet net from brains |
| `L1 (pin 2) -> C1 (pin 1)` **and** `L1 (pin 2) -> E1 (pin 1)` | feed node |
| `C1 (pin 2) -> GND` | |
| `E1 (GND pads) -> GND` | antenna ground + keep-out |

### sound (new sheet, 19-08-2026)

| Wire | Notes |
| ---- | ----- |
| `BZ1 (pin 1 +) -> Q1 (drain/collector)` | [ADD Q1] NPN/MOSFET driver — **active** buzzer draws ~20–40 mA, too much for a GPIO pin |
| `Q1 (source/emitter) -> GND` | |
| `Q1 (gate/base) -> buzzer GPIO -> 100 kΩ -> GND` | [ADD] pull-down so the buzzer can't click at boot |
| `BZ1 (pin 2 −) -> 3.3V` | (or − to GND with Q1 high-side — pick one, then invert the firmware) |
| `Buzzer GPIO -> 3.3V` | [ADD] 100 nF across BZ1 pins (active buzzer), or just rely on the driver |

Buzzer choice — recommendation (see conversation 19-08-2026):

- **BZ1 placed = SparkFun 9 mm SMD "active" buzzer** (internal oscillator): fixed shrill
  ~2.7 kHz tone, on/off only — no chirps/melodies, needs the transistor driver (~20–40 mA).
  Works if a single monotone beep is acceptable.
- **Preferred for a cat finder: passive piezo transducer (~9–12 mm, e.g. Murata
  PKLCS1212E4001-R1)** — the ESP32-C3 drives it directly with LEDC PWM (2 mA), and
  firmware gets programmable chirps/patterns ("find me" cadence, low-battery tone) instead
  of one fixed beep. Lighter, no transistor, no coil whine at 3.3 V. Cost ≈ same.
- **Modem-codec alternative (only if loud + melodic matters):** SIM7670G exposes EAR/MIC
  pins — a small speaker could ring tones via AT. Overkill here: modem must be awake
  (100 mA class) and it adds an audio path for a finder beep.

## Components to add (summary)

| [ADD] | Value | Where it plugs |
| ----- | ----- | -------------- |
| Decaps ×7 | 100 nF 0402 | U5 pins 2, 11, 17, 18, 31; U2 pin 9; U1 pin 30 |
| C_IN | 1 µF 0402/0603 | U3 pin 1 (V_IN) — ME6211 input cap |
| C_OUT | 10 µF 0603 | U3 pin 5 (V_OUT, 3.3 V rail) — LDO stability + bulk |
| C_VBUS | 10 µF 0603 (recommended) | J2 VBUS side |
| Bulk caps ×2 | 22 µF 0603/0805 | U4 pin 7 (VIN), U4 pin 3 (VOUT) |
| Modem bulk | 22 µF + 100 nF | U1 pins 55/56/57 (VBAT) |
| Small decaps | 1 µF ×2 | U1 pin 97 (1V8_GNSS), U1 pin 99 (VDD_2V8) |
| R_EN | 10 kΩ 0402 | U5 pin 7 -> 3.3V — **DONE ✓ (R4)** |
| R_BOOT | 10 kΩ 0402 | U5 pin 15 -> 3.3V — **DONE ✓ (R3)** |
| SW_RST | tact switch SMD 4.5×4.5 mm | U5 pin 7 (CHIP_EN) ↔ GND — **DONE ✓ (SW2)** |
| SW_BOOT | tact switch SMD 4.5×4.5 mm | U5 pin 15 (GPIO9) ↔ GND — **DONE ✓ (SW1)** |
| J3 | 1×6 pin 2.54 mm THT header (or 6 test pads) | 3V3, GND, U0TXD (pin 28), U0RXD (pin 27), CHIP_EN (pin 7), GPIO9 (pin 15) |
| R_PROG | 1.2–2.4 kΩ 0402 | U6 pin 2 -> GND |
| FB divider R3/R4 | 700 kΩ / 100 kΩ 0402 | U4 VOUT -> FB -> GND (4.0 V rail) — ⚠ the 5V symbol on U4 VOUT must become VBAT_MODEM first |
| L2 | 1 µH 0603 | U4 pin 2 SW <-> CELL_POS |
| Q1 | 8205A SOT-23-6 (unpopulated) | U7 gates, see li-ion sheet |
| Q2 (buzzer driver) | NPN 2N7002/S8050 or N-MOSFET | BZ1 (only if keeping the active buzzer) |
| PWRKEY cap | 100 nF 0402 | U1 pin 1 PWRKEY -> GND — auto-power-on, frees GPIO3 for the buzzer (PROPOSED) |
| Jumper | 2-pad solder jumper (bridged) | P_NEG <-> GND |
| U.FL + antenna | U.FL + LTE flex | U1 pin 60 |
| GNSS patch | 25×25 mm passive | U1 pin 90 |
| Optional | 32 kHz xtal — **DONE**, charge LEDs, NTC, 1PPS, ESD USBLC6-2SC6, status LED | per rows above |

## GPIO allocation (PROPOSED — all 9 free GPIOs of the C3 QFN-32)

| U5 pin | GPIO | Assigned to |
| ------ | ---- | ----------- |
| 27 | 20 | U1 pin 9 (TXD) |
| 28 | 21 | U1 pin 10 (RXD) |
| 14 | 8 | U2 pin 4 (SDA) |
| 15 | 9 | U2 pin 1 (SCL) — strapping pin, keep R_BOOT pull-up |
| 16 | 10 | U2 pin 12 (INT1, motion wake) |
| 6 | 2 | U1 pin 51 (VBAT_ADC) — strapping pin, OK as ADC input |
| 8 | 3 | U1 pin 1 (PWRKEY) — **alternatives for the buzzer:** (a) 100 nF auto-power-on cap on PWRKEY frees GPIO3 → buzzer; (b) run the LED off U1 pin 66 (STATUS) directly and free GPIO18 → buzzer |
| 25 | 18 | U1 pin 66 (STATUS) — or direct LED drive, see GPIO3 row |
| 26 | 19 | U4 pin 5 (EN, modem rail) |

> **Buzzer GPIO — still undecided.** All 9 GPIOs are allocated; the buzzer needs one.
> Recommended path: **100 nF PWRKEY → GND (auto power-on, a documented SIM7670G wiring)
> and free GPIO3 for the buzzer** — the modem rail is already gated by GPIO19/EN, and
> the 100 nF option is a standard SIMCom app-note technique (delayed self-hold on the
> internal pull-up). Firmware then just toggles the GPIO for the beep.

Pins 9–13 (MTMS/MTDI/MTCK/MTDO), 19–24 (SPI*: internal flash) and 4/5
(XTAL_32K) are not available on this package. That leaves nothing for UART1
(GNSS_NMEA) — intentional: NMEA is read via `AT+CGNSSINFO` on UART0.

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
