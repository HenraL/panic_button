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
-- LAST Modified: 0:40:0 19-08-2026
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
| power_input | J2 | USB-C charge-only | done ✓ |
| power_input | U3 | ME6211C33M5 (3.3 V LDO) | done ✓ |
| power_input | R1, R2 | 5.1 kΩ CC pull-downs | done ✓ |
| li-ion_charging_handling | U6 | TP4056-42-ESOP8 charger | to wire |
| li-ion_charging_handling | U7 | DW01A protection | to wire |
| li-ion_charging_handling | BT1 | `Device:Battery_Cell` (1000 mAh) | to wire |
| modem_rail | U4 | TPS61022 boost (modem rail) | to wire |
| network_and_gps | U1 | SIM7670G-LNGV modem | to wire |
| network_and_gps | J1 | JAE SF72S006 SIM holder | to wire |
| accelerometer | U2 | LIS2DE12 (design says LIS2DW12 — OPEN) | to wire |
| radio_layout | E1 | 2450AT18A0100001E chip antenna | to wire |
| radio_layout | L1, C1 | 4.7 nH + 1.2 pF BLE match | to wire |

## Wiring — already done (verified by scan, 19-08-2026)

**brains (15 wires):**

- `U5 (pin 30 XTAL_P) -> Y1 (pin 1)` via C2 ✓
- `U5 (pin 29 XTAL_N) -> Y1 (pin 3)` via C3 ✓
- `Y1 (pin 2 case GND) -> GND` and `Y1 (pin 4 case GND) -> GND` ✓
- `C2 (pin 2) -> GND` and `C3 (pin 1) -> GND` (caps midpoint) ✓
- `U5 (pin 33 GND) -> GND` ✓
- `U5 (pin 2 VDD3P3) -> 3.3V` ✓

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
| ---- | ----- |
| `U5 (pin 11 VDD3P3_RTC) -> 3.3V` | + 100 nF decap [ADD] |
| `U5 (pin 17 VDD3P3_CPU) -> 3.3V` | + 100 nF decap [ADD] |
| `U5 (pin 18 VDD_SPI) -> 3.3V` | + 100 nF decap [ADD] |
| `U5 (pin 31 VDDA) -> 3.3V` | + 100 nF decap [ADD] |
| `U5 (pin 7 CHIP_EN) -> R_EN (pin 1)` | 10 kΩ [ADD]; `R_EN (pin 2) -> 3.3V` |
| `U5 (pin 15 GPIO9) -> R_BOOT (pin 1)` | 10 kΩ [ADD, optional]; `R_BOOT (pin 2) -> 3.3V` — doubles as the I2C SCL pull-up |
| `U5 (pin 1 LNA_IN) -> L1 (pin 1)` | cross-sheet to radio_layout |
| `U5 (pin 27 U0RXD) -> U1 (pin 9 TXD)` | modem AT UART |
| `U5 (pin 28 U0TXD) -> U1 (pin 10 RXD)` | modem AT UART |
| `U5 (pin 14 GPIO8) -> U2 (pin 4 SDA/SDI)` | I2C SDA (PROPOSED) |
| `U5 (pin 15 GPIO9) -> U2 (pin 1 SCL/SPC)` | I2C SCL (PROPOSED) |
| `U5 (pin 16 GPIO10) -> U2 (pin 12 INT1)` | motion wake (PROPOSED) |
| `U5 (pin 6 GPIO2) -> U1 (pin 51 VBAT_ADC)` | battery voltage sense (PROPOSED) |
| `U5 (pin 8 GPIO3) -> U1 (pin 1 PWRKEY)` | power-on pulse (PROPOSED) |
| `U5 (pin 25 GPIO18) -> U1 (pin 66 STATUS)` | modem status / LED (PROPOSED) |
| `U5 (pin 26 GPIO19) -> U4 (pin 5 EN)` | modem rail switch (PROPOSED) |
| `U5 (pin 4/5 XTAL_32K) -> 32.768 kHz crystal` | [ADD, optional] — skip, C3 internal RC is fine |

### power_input

| Wire | Notes |
| ---- | ----- |
| `U3 (pin 3 CE) -> 3.3V` | enable the LDO |
| `U3 (pin 4 NC)` | leave unconnected |

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

| Wire | Notes |
| ---- | ----- |
| `U4 (pin 7 VIN) -> CELL_POS` | + 22 µF decap [ADD] |
| `U4 (pin 2 SW) -> L2 (pin 1)` | [ADD] 1 µH; `L2 (pin 2) -> CELL_POS` |
| `U4 (pin 3 VOUT) -> VBAT_MODEM` | + 22 µF decap [ADD] |
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
| `U1 GND pins (39×) -> GND` | stubs already on pins 2, 17, 18, 29, 39, 45, 46, 54, 58, 59, 61, 62 (left), 63–65, 69–89, 91, 92, 94 (right) — just drop a GND symbol / label on the bus |

### accelerometer

| Wire | Notes |
| ---- | ----- |
| `U2 (pin 9 Vdd) -> 3.3V` | + 100 nF [ADD] |
| `U2 (pin 10 Vdd_IO) -> 3.3V` | |
| `U2 (pin 6 GND) -> GND` | |
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

## Components to add (summary)

| [ADD] | Value | Where it plugs |
| ----- | ----- | -------------- |
| Decaps ×7 | 100 nF 0402 | U5 pins 2, 11, 17, 18, 31; U2 pin 9; U1 pin 30 |
| Bulk cap | 10 µF 0603 | U3 pin 5 (V_OUT, 3.3 V rail) |
| Bulk caps ×2 | 22 µF 0603/0805 | U4 pin 7 (VIN), U4 pin 3 (VOUT) |
| Modem bulk | 22 µF + 100 nF | U1 pins 55/56/57 (VBAT) |
| Small decaps | 1 µF ×2 | U1 pin 97 (1V8_GNSS), U1 pin 99 (VDD_2V8) |
| R_EN | 10 kΩ 0402 | U5 pin 7 -> 3.3V |
| R_BOOT | 10 kΩ 0402 (optional) | U5 pin 15 -> 3.3V |
| R_PROG | 1.2–2.4 kΩ 0402 | U6 pin 2 -> GND |
| FB divider R3/R4 | 700 kΩ / 100 kΩ 0402 | U4 VOUT -> FB -> GND (4.0 V rail) |
| L2 | 1 µH 0603 | U4 pin 2 SW <-> CELL_POS |
| Q1 | 8205A SOT-23-6 (unpopulated) | U7 gates, see li-ion sheet |
| Jumper | 2-pad solder jumper (bridged) | P_NEG <-> GND |
| U.FL + antenna | U.FL + LTE flex | U1 pin 60 |
| GNSS patch | 25×25 mm passive | U1 pin 90 |
| UART pads | 6 pads (3V3, GND, U0TXD, U0RXD, CHIP_EN, GPIO9) | U5 pins 28, 27, 7, 15 |
| Optional | 32 kHz xtal, charge LEDs, NTC, 1PPS, ESD USBLC6-2SC6, status LED | per rows above |

## GPIO allocation (PROPOSED — all 9 free GPIOs of the C3 QFN-32)

| U5 pin | GPIO | Assigned to |
| ------ | ---- | ----------- |
| 27 | 20 | U1 pin 9 (TXD) |
| 28 | 21 | U1 pin 10 (RXD) |
| 14 | 8 | U2 pin 4 (SDA) |
| 15 | 9 | U2 pin 1 (SCL) — strapping pin, keep R_BOOT pull-up |
| 16 | 10 | U2 pin 12 (INT1, motion wake) |
| 6 | 2 | U1 pin 51 (VBAT_ADC) — strapping pin, OK as ADC input |
| 8 | 3 | U1 pin 1 (PWRKEY) |
| 25 | 18 | U1 pin 66 (STATUS) |
| 26 | 19 | U4 pin 5 (EN, modem rail) |

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
| 8205A dual MOSFET | SnapMagic (SnapEDA) | UMW 8205A, SOT-23-6, ~$0.07 |
| LTE flex antenna | SnapMagic / Ali | Taoglas FXUB63 family; U.FL pigtail variants at LCSC/Ali |
| GNSS patch | SnapMagic / LCSC | 25.4×25.4×4 mm passive, e.g. Taoglas CGGBP.25.4.A.02 |
| 2450AT18A0100001E (BLE) | already integrated | lib `2450AT18A0100001E`, E1 placed |

Search SnapMagic with the MPN (`U.FL-R-SMT-1`, `8205A UMW`, `FXUB63`,
`CGGBP.25.4.A.02`) and pick the entry whose package picture matches; prefer
entries with symbol+footprint+3D.