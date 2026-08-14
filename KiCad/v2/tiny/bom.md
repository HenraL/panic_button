# BOM — panic_button_tiny (KiCad v2, "tiny" variant)

Live bill of materials as of 14-08-2026. Prices are rough single-unit estimates (LCSC /
Aliexpress / JLCPCB, USD) — verify before ordering. Cost target: **< $50 / unit, excluding
the IoT/SIM plan** (see `design.md` for all decisions and notes).

## Estimated BOM

| Qty | Ref | Part | Est. price | Notes |
|-----|-----|------|-----------:|-------|
| 1 | U1 | ESP32-C3FH4 (QFN-32 5x5, 4MB flash) | $1.50–2.50 | MCU + BLE 5.0 advertising; boot = GPIO9, reset = EN |
| 1 | U2 | SIM7670G LTE Cat1 module | $10–20 | 4G, GNSS, SMS + HTTP/HTTPS + MQTT via AT; IoT M2M SIM |
| 1 | U3 | Li-ion charge IC (TP4054 / TP4056 / IP5306) | $0.20–0.60 | USB-C charging of the cell |
| 1 | – | Li-ion pouch cell, 3.7 V (300–800 mAh) | $2–5 | size driven by 4G TX bursts + sleep budget |
| 1 | – | USB-C receptacle | $0.20–0.60 | charge in; optional D+/D- to MCU for flashing |
| 1 | U4 | MEMS accelerometer (LIS2DW12 / MMA8452) | $0.50–1.50 | wake-on-motion interrupt → "if movement" trigger |
| 1 | – | LTE antenna (flex PCB, IPEX) | $0.50–2 | |
| 1 | – | GNSS antenna (passive/active) | $1–4 | feeds SIM7670G GNSS input |
| 1 | – | 3.3 V LDO (ME6211 / XC6206) + decoupling | $0.10–0.30 | MCU rail |
| 1 | – | Modem supply: boost to ~4 V or heavy bulk caps + load switch | $1–3 | SIM7670G needs 3.4–4.3 V (typ. 4 V) with ~2 A TX peaks; switchable rail for deep sleep |
| – | – | LEDs (status: charge / fix / alert) + resistors | $0.30–1 | |
| – | – | Tact switches (boot / reset), headers, passives | $0.50–1.50 | |
| 1 | – | PCB (JLCPCB, 5 pcs) | $2–5 | |
| – | **Total** | | **~$20–45** | inside the < $50 target |

## Tracking

Keep this file in sync with the schematic (`panic_button_tiny.kicad_sch`) — currently only
U1 (ESP32-C3, QFN-32 5x5) is placed.