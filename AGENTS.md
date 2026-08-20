<!-- 
-- +==== BEGIN AsperHeader =================+
-- LOGO:
-- @@@@@@@@@@@@@@@@@@@@@@@@@@@@
-- @:.......................@##@@@
-- @:.......................@..-#@@@
-- @:.......................@....=@@@
-- @:.......................@......*@@@
-- @:.......................@........@@@
-- @:.......................++++++++++%@
-- @:.................................-@
-- @:.+@@@@@@@@@@@@@@@@@@@@@@@@@@@@@=.-@
-- @:.................................-@
-- @:.................................-@
-- @:.+@@@@@@@@@@@@@@@@@@@@@@@@@@@@@=.-@
-- @:.................................-@
-- @:.................................-@
-- @:.-#############################:.-@
-- @:.=%%%%%%%%%%%%%%%%%%%%%%%%%%%%%-.-@
-- @:.................................-@
-- @:........................-=====...+@
-- @:.+@@@@@@@@@@@@@@.....:===========#@
-- @:....................-==============
-- @:....................===============
-- @:.+@@@@@@@@@@@@@@....===============
-- @:.....................-===========+*
-- @:.....................:===========#@
-- @@@@@@@@@@@@@@@@@@@@@@@@@%+=====#@@@@
-- /STOP
-- PROJECT: AsperHeader
-- FILE: AGENTS.md
-- CREATION DATE: 03-08-2026
-- LAST Modified: 19:20:23 20-08-2026
-- DESCRIPTION:
-- A small panic beacon (target size: no bigger than a finger, a palm if that can't be reached) that
-- reports its position to a server, falls back to SMS when no server is reachable, and always
-- advertises a BLE beacon (Apple Find My-style, Android hub, Samsung if they deviate) for app-less
-- phone interaction. Apple UWB is out of reach (proprietary U1/U2 + MFi).
-- /STOP
-- COPYRIGHT: (c) Asperguide
-- PURPOSE: This is the file in charge of providing instructions for any ai agent that is asked to intervene on the program.
-- // AR
-- +==== END AsperHeader =================+
-->
# AGENTS.md

Rules and norms for AI coding agents working in this repository.
This file documents project-specific conventions, rules, and non-obvious gotchas
that AI coding assistants must follow when working on this codebase.

## Hard rules (from the repository owner)

- **NEVER run any git command that alters the repository.** The repo is the owner's lifeline.
  - Forbidden: `commit`, `push`, `pull`, `rebase`, `merge`, `checkout/swtich`, `reset`, `revert`, `clean`, `tag`, `mv`, `rm`, `branch -d`, config changes, anything that writes refs/index/HEAD/working tree.
  - Allowed (read-only inspection only): `git status`, `git log`, `git diff`, `git show`, `git branch`, `git remote -v`.
- Only commit / stage / push when the user explicitly asks. The user handles all version control operations manually.
- Everything happens *inside* the repository folder.
- Never run Python scripts with system Python. Python dependencies come from virtual environments:
  - Project venv `lenv/`: use `./lenv/bin/python` (and `./lenv/bin/pip`) instead of a system Python. `lenv/` is git-ignored, so never rely on it in code, docs, or scripts — build scripts must `pip install -r requirements.txt` into the active env.
  - Tool venvs: `bonus/font2c.sh` and `bonus/img2c.sh` create their own venv (`bonus/font2c_env/`, `bonus/img2c_env/`) from their own `requirements.txt` and handle activation/deactivation automatically. No venvs are shipped with the repository.
- `config/.env` and `in/file_info.json` are sensitive/git-ignored. Never log, print, or commit their contents. Reference the `sample.*` fixtures for structure.
- Never create files inside the git-ignored input (`in/frais/**`, `in/sci/**`, actual `in/file_info.json`) or output (`out/**`) unless the task is explicitly about sample data or generating output.
- The `todo.todo` file in the repo root is the owner's personal progress tracker. Never alter, delete, or read it into code. Leave it untouched unless the owner explicitly asks otherwise.
- Never hand-edit generated files. Files under `lib/fonts/` and `lib/images/` are **generated** by the Python converters — always modify the converter (`bonus/font2c/convert.py` or `bonus/img2c/convert.py`) and re-run. Hand-edits will be overwritten.

## Project overview

A **panic beacon** — a small device (target: no bigger than a finger; a palm if that size can't be reached) that:

1. Updates its position towards a server.
2. If that is not available, sends an SMS (or similar).
3. Regardless of the above, advertises a BLE beacon (Apple Find My-style, Android hub, and Samsung if they stray from the others) so phones can interact with it without needing to maintain an app. Apple UWB was dropped — it requires Apple's proprietary U1/U2 chip + MFi program.

## Hardware / BOM

- Active design: `KiCad/v2/tiny/panic_button_tiny` (KiCad 10, v2 "tiny" variant).
- Do **not** edit KiCad schematics/PCBs (`*.kicad_sch`, `*.kicad_pcb`) — the owner handles the design. Creating new library symbols/footprints is the only allowed exception.
- **Schematic change proposals: ASCII only, or a sibling file — never in place.** Even when the owner asks you to "update the schematic", never overwrite their `*.kicad_sch`/`*.kicad_pcb`: draw the wiring as an ASCII diagram in the response (and/or the ledger), or produce a modified copy next to the original (e.g. `modem_rail.FIXED.kicad_sch`) so the owner can compare and apply it themselves in KiCad. The owner applies all schematic edits by hand.
- Live BOM: `KiCad/v2/tiny/bom.md` — strictly the bill of materials, prices are estimates; design decisions / ideas / open questions: `KiCad/v2/tiny/design.md`. Keep both in sync whenever the BOM or decisions change.
- Platform: **ESP32-C3** (MCU + BLE 5.0) + **SIM7670G** LTE Cat1 module (4G, built-in GNSS, SMS + HTTP via AT commands, IoT M2M SIM).
- Motion: MEMS accelerometer with wake-on-motion interrupt wakes the MCU from deep sleep ("if movement" trigger) — neither the C3 nor the modem senses motion.
- Power: Li-ion 3.7 V + USB-C charge IC. Modem rail must sustain ~2 A TX bursts (boost to ~4 V or heavy bulk caps) and be switchable for deep sleep.
- Cost target: < $50 / unit, excluding the IoT plan.
- No UWB (see above); phone interaction = BLE only, app-less (Apple Find My accessory, Android Find My Device hub, Samsung SmartThings Find).
- Firmware lives in `platformio/` and comes **after** the circuit planning — it is not a parallel effort.
- Any virtual environment / tool environment goes in `bonus/`; throwaway scratch files go in `bonus/tmp/` (survives reboots, but treat it as disposable).

## KiCad schematic verification (connection & pin checks)

The schematic is the owner's working file — agents only *read* it, and must read it the way
KiCad actually resolves it, or connection claims will be wrong. These rules were learned
the hard way (wrong pin geometry → wrongly claimed nets were "dead-ended"):

- **Pin coordinates come from the embedded symbols, never from external library copies.**
  Each `.kicad_sch` embeds its own `(symbol "LIB:NAME_0_0" ...)` blocks with the pin
  definitions; the instance `(at x y rot)` positions the whole thing. A copy of the same
  symbol in another library (e.g. `my_kicad_mods/`) may have *different geometry* — and the
  project resolves library names through the **global** sym-lib-table
  (`~/.config/kicad/<ver>/sym-lib-table`) when the lib isn't in the project table (e.g.
  `MCU_Espressif` → `${KICAD9_SYMBOL_DIR}`). Always extract pins from the embedded blocks
  inside the schematic file itself.
- **A connection exists only where a wire terminates exactly on a pin tip.**
  Absolute tip = instance `(at)` + pin `(at dx dy angle)` (+ `(length)` in the pin's
  direction). **Y sign flip: embedded symbol blocks are written in the library's Y-up
  convention while the schematic plane is Y-down — the rendered tip offset is `(dx, -dy)`
  (before rotation).** Skipping this flip mirrors every pin tip and wrongly "moves"
  connections (this happened twice: crystal nets falsely reported on GPIO8/GPIO9 and a
  falsely reported VDD3P3_CPU→GND short). Verify with the owner's UI, not with arithmetic
  alone. Never declare a net "unconnected" or "dead-ending" without computing every pin
  tip of the involved symbols against the wire endpoints.
- **Pins are not placed on a strict grid.** Symbol authors leave uneven gaps to visually
  group related pins (power pins along one edge, XTAL pins grouped, GPIO runs, etc.) —
  e.g. XTAL_N may sit 500 mils away from its neighbor. Never infer a pin's identity or
  position from arithmetic ("next pin = +100 mils", "odd pins are X") — always read the
  `(number)`/`(name)` pair from the embedded block and compute each tip from its own
  `(at)`.
- **KiCad writes the file only on save.** Scans reflect the last save, not the editor.
  Before concluding anything about current state, check file mtimes and the `~*.lck` lock
  files in the project dir; if KiCad is open with unsaved edits, ask the user to save
  first, then re-scan.
- **The owner's display unit is mils** (1 mil = 0.0254 mm) — switched to mm on 17-08-2026;
  confirm the current unit each session. Always quote coordinates in **mils first** when
  discussing wiring (e.g. "pin 29 tip at (3150, 2400) mils = (80.01, 60.96) mm") — the
  owner does not convert on the fly.
- **Reference designators drift.** The owner renumbers freely (caps C1/C2 → C2/C3, etc.);
  never assume designators persist between sessions — re-scan `Reference`/`Value` pairs
  before quoting them, and refer to the chip as `U5` (ESP32-C3), never "C3".
- Component pin numbers/names must also be read from the embedded blocks (e.g. the
  C3E40000121010X crystal: pins 1+3 = XTAL, pins 2+4 = case GND), then cross-checked
  against the symbol instance position.
- Wiring/placement state of every sheet is tracked in `KiCad/v2/tiny/missing_components.md`
  (checklist + pin-level wiring reference) — keep it in sync whenever the schematic state
  changes, and verify claims against the current file before editing it.

## Golden Rules

### 1. `using` is banned

Every type must be accessed via its full qualified name. No `using namespace std;`,
no `using namespace BakedFonts;`, no `using std::vector;`, no `using` of any kind.
Qualify everything explicitly.

### 2. No `static` variables in .cpp files

All state must live in class member fields. File-scope `static` variables are not allowed.
Shared runtime state is communicated through class members or pointer arguments, never via file-scope variables.

### 3. Debug serial uses `SharedInstances::serial.serial_print()`

Never `Serial.print()`. Always go through the shared serial instance.

### 4. `constexpr` is banned

Use `static const` inside `namespace` instead. `constexpr` can trigger undefined behaviour on
certain embedded compilers (ESP8266, some GCC/architecture combinations). Use `static const`
at namespace scope — it's safe and gives the same compile-time constant semantics.

## Conventions to follow in this codebase

### File headers

Every source file starts with a header block:

- **Python files**: EPITECH-style block, match exactly (only `filename` changes):

```
##
# (or one `#` in some files) EPITECH PROJECT, 2022
# quittances
# File description:
# <filename>.py
##
```

- **C++ files**: ASCII-art banner block (see existing files for template).

### Naming

- Namespaces, classes, structs, enum types (C++) and classes (Python): `PascalCase` → `My::LCD::Display`, `BakedFonts::FontHandle`, `Create_PDF`, `Load_Tables`, `Window_Tools`.
- Functions, methods, variables: `snake_case` → `set_baked_font()`, `fill_rect()`.
- Constants: `UPPERCASE` (Python) / `UPPER_SNAKE_CASE` for config/pin constants, enums, macros (C++) → `LCD_CS`, `STYLE_NORMAL`.
- Private member fields (C++): leading underscore → `_u8g2`, `_baked_font`.
- Font handle variables: `snake_case` + `_handle` suffix → `tiny5_8pt_handle`; font bit data arrays: `snake_case` + `_bits` suffix → `tiny5_8pt_regular_bits`.
- In package `__init__.py`, modules are re-exported as short uppercase aliases (`CHTML`, `CPDF`, `CTXT`, `LT`, `LENV`, `BFILE`, `CONST`, `WT`, `HS`, `EM`).
- Class-level attributes are set in `__init__`, typed with inline hints.

### Style & formatting

- Linter: VSCode with the **Pylance** extension (enforces PEP 8 + type-checking). Keep code Pylance-clean.
- Formatter: **autopep8** (`line-length` ~120). Indentation: 4 spaces, no tabs, no trailing whitespace.
- Markdown files (`.md`) are linted with the **markdownlint VSCode extension** (default rules, no repo config file). Every multiline code fence must carry a language tag (` ```txt ` for ASCII/plain blocks, ` ```python `, etc.). Long lines in wiring tables and ASCII diagrams are intentional (MD013 tolerated there); avoid duplicate headings (MD024), bare URLs (MD034), and trailing whitespace (MD009).
- Braces (C++): Allman (own line) for namespaces/classes/functions; K&R (same line) for control flow (`if`/`for`/`while`).
- Include guards: `#pragma once` everywhere.
- Variable declarations: declare all local variables at the top of the function body — never in the middle of the function or inside loop bodies.
- **No ternaries; never "fold" code for memory/compactness — legibility wins over compactness.** Prefer a full `if/else` block and keep one logical step per line. In C++, only simple value-expression ternaries (e.g. `int x = cond ? a : b;`) are allowed; ternary `? :` with brace bodies is banned.
- Existing code wraps comparisons/bool checks in parens with explicit bools (`if (x == True):`, `if (has_errors == False):`). This is a **legacy C/C++ habit**, not a rule — don't be surprised to see it in old code, but don't force it in new code either (plain `if x == True`/`if x` is fine).
- Type hints on every method/arg (e.g. `list[list, list]`, `dict[str, str]`, `any`). Keep them loose (the codebase uses `any` for widgets/frames, `list[list, list]` "matrix" convention).
- Docstrings for every method.
- Keep string open/close boilerplate (HTML `<td>`, `\t`, spacing constants) in `__init__` attributes like the `Create_HTML` / `Create_TXT` classes do.
- Config-driven colors/geometry come from `config/config.json` (`tkinter_window_decoration`).
- Files are read with explicit `encoding=self.encoding` (`"utf-8"` default).
- Functional logic and test/demo blocks: modules may end with `if __name__ == "__main__":` demo blocks that generate sample PDFs (see `src/pdf_tools/`). You may run these with UPPERCASE python from repo root.

### Language

- English for code comments, docstrings, and commit-relevant text.
- French for end-user visible strings (UI labels like "OK", "Annuler", month names "Janvier"..., document title "QUITTANCE DE LOYER").
- Dates: `dd/mm/yyyy` everywhere, separated by `/`.

### Namespace layout

- Application code: `My::` → `My::Config::`, `My::LCD::`, `My::LED::`, etc.
- Font library: `BakedFonts::` → `BakedFonts::Size::`, `BakedFonts::Family::`, `BakedFonts::Helpers::`
- Library code: top-level namespaces like `Screen`, `Matrix`, `FileExplorer`
- Namespace closing comments required: `} // namespace Foo`

### PROGMEM

Large read-only data (font bitmaps, lookup tables, message sequences) must use `PROGMEM`.
Read back with `pgm_read_byte()`, `pgm_read_ptr()`, or `memcpy_P()`.

### File naming

- Headers: `.hpp`
- Sources: `.cpp`
- Config headers: `include/my/config/<name>.hpp`

### Library structure

Every library follows a consistent layout. For a library named `<libname>` in namespace `<Libname>`:

```
lib/<libname>/
├── include/
│   ├── <libname>.hpp           — public forwarder: #include "internal/<libname>.hpp"
│   └── internal/
│       ├── <libname>.hpp       — main header (class, public API)
│       ├── constants.hpp       — magic numbers, limits, pin defs
│       ├── structs.hpp         — struct/ POD definitions
│       └── ...                 — any additional headers as needed
├── src/
│   ├── <libname>.cpp           — implementation
│   └── ...                     — any additional .cpp files
```

- The public `<libname>.hpp` is a single-line forwarder so users write `#include <libname.hpp>`.
- No magic numbers inline — everything goes in `constants.hpp`.
- Enclose the library in a namespace matching the library name (e.g. `namespace SDCard { ... }`).
- Values that cannot be known at compile time must be constructor or function parameters, never hardcoded globals.

### Git / commits (when the user explicitly authorizes)

- Commit message convention (from history): conventional headers in brackets, upper-case type:
  - `[ADD] <short description>`
  - `[UPDATE] <description>`
  - `[FIX] <description>`
  - `[REMOVE] <description>`
  - e.g. `[UPDATE] the .gitignore to retain the sample files`
- Subject line + short body, imperative mood, lower-case after type.

### Testing / running

- No test framework. Verify by running `./lenv/bin/python main.py` (GUI) or the `if __name__ == "__main__":` demo blocks to confirm no crash.
- After a code change, at minimum run: `./lenv/bin/python -m py_compile <file>` on modified files.
- Installing new deps: always via `./lenv/bin/pip` and record them in `requirements.txt` with `==` pinning.

## Agent hygiene

- Explore before editing: use Read/Grep/Glob; the `lenv` bin for python.
- Check existing code for patterns before writing new code.
- Don't add dependencies not already present unless you really need the new package AND keep `requirements.txt` in sync.
- Never leave `__pycache__` or *.pyc additions in git-ish visibility (they are ignored).
- When in doubt, ask the user before performing an irreversible action.
- This file was written precisely to answer these questions — read it first.
