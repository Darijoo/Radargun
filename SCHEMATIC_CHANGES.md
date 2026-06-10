# Schematic Changes Checklist

Use this file to track the changes you need to make in KiCad before PCB layout.

---

## 🔴 MUST FIX (Electrical Issues)

### 1. Change R17 (Blue Status LED Resistor) Value
- **Current**: 1kΩ
- **Problem**: Blue LEDs have ~3.0-3.2V forward voltage. At 3.3V from GPIO46: `(3.3 - 3.0) / 1000 = 0.3mA` → LED will be invisible.
- **Fix**: Change R17 value to **100Ω** (gives ~1.5mA) or **220Ω** (gives ~1.4mA). Either works for a high-efficiency blue LED.
- **How in KiCad**: Double-click R17 → change Value field from `1k` to `100` or `220`.
- [x] Done

### 2. Change R16 (Red Power LED Resistor) Value
- **Current**: 1kΩ
- **Problem**: At 3.3V with ~2.0V red LED: `(3.3 - 2.0) / 1000 = 1.3mA` → works but is dim.
- **Fix**: Change R16 value to **330Ω** (gives ~3.9mA) or **470Ω** (gives ~2.8mA) for comfortable visibility.
- **How in KiCad**: Double-click R16 → change Value field from `1k` to `330` or `470`.
- [x] Done

### 3. Verify HB100 Decoupling Caps (C12, C13) Are On Correct Net
- **What to check**: C12 (10µF) and C13 (0.1µF) must connect to the `HB100_VCC` net (the SWITCHED power after Q1 AO3401A), not the `5V_BOOST` rail.
- **How to check**: Click on C12 and C13 in the schematic. Follow their positive terminal wire — it should go to the same net as Q1's Drain and the `HB100_VCC` label, NOT to the `+5V` power symbol.
- **If wrong**: Move the caps so they connect between `HB100_VCC` and `GND`.
- [x] Verified correct / Fixed

---

## 🟡 RECOMMENDED (Improve Reliability)

### 4. Add External Pull-Up Resistors on IO9 and IO10
- **Why**: IO9 (`ACTION_BTN`) and IO10 (`MODE_SEL`) are button/switch inputs that currently rely on ESP32 internal pull-ups (~45kΩ). The HB100 radar sensor emits 10.525 GHz microwaves that can couple noise. External pull-ups improve noise immunity.
- **What to add**:
  - Add a **10kΩ resistor** from IO9 (`ACTION_BTN` net) to `+3.3V`
  - Add a **10kΩ resistor** from IO10 (`MODE_SEL` net) to `+3.3V`
- **How in KiCad**: Place two new `Device:R` symbols (10kΩ, 0805 footprint). Wire one end to the button/switch net and the other end to the `+3.3V` power symbol.
- [x] Done (or skipped — internal pull-ups may be fine for your use case)

### 5. Visually Verify AO3401A (Q1) Source/Drain Orientation
- **What to check**: In a P-channel high-side switch:
  - **Source** (Pin 2) must go to `5V_BOOST` (the supply)
  - **Drain** (Pin 3) must go to `HB100_VCC` (the load)
  - **Gate** (Pin 1) must go to the 2N7002 drain / pull-up resistor
- **How to check**: Click on Q1 in the schematic. Hover over each pin and confirm the net names match the above.
- [x] Verified correct

---

## 🟢 OPTIONAL (Nice to Have)

### 6. Add Test Points for Debug
- Place KiCad `TestPoint` symbols on these nets for easy probing during board bring-up:
  - `3V3`
  - `5V_BOOST`
  - `BAT+`
  - `HB100_ADC`
- **How**: Place symbol `Connector:TestPoint` from KiCad library, wire to each net. Use a small SMD pad footprint like `TestPoint:TestPoint_Pad_1.0x1.0mm`.
- [ ] Done (or skipped)

### 7. Run ERC (Electrical Rules Check)
- **When**: After all the above changes are made.
- **How**: In KiCad Schematic Editor → Inspect → Electrical Rules Checker → Run ERC.
- **Expected**: Should pass with 0 errors. Warnings about unconnected pins are OK if they have no-connect flags.
- [ ] ERC passed clean

---

## 📝 After Schematic Fixes — Footprint Assignment

These components need footprints assigned. Choose your specific parts and assign in KiCad via **Tools → Edit Symbol Fields** or by editing each symbol's properties individually.

| Ref | Component | Suggested Footprint Options |
|-----|-----------|----------------------------|
| D2 | Red Power LED | `LED_SMD:LED_0805_2012Metric` / `LED_THT:LED_D3.0mm` |
| D3 | Blue Status LED | `LED_SMD:LED_0805_2012Metric` / `LED_THT:LED_D3.0mm` |
| L1 | 22µH Inductor | Depends on chosen part. Common: `Inductor_SMD:L_1210_3225Metric` |
| BT1 | Li-Po Battery | JST PH connector: `Connector_JST:JST_PH_B2B-PH-K_1x02_P2.00mm_Vertical` |
| SW1 | RESET Button | `Button_Switch_SMD:SW_SPST_B3U-1000P` / `Button_Switch_THT:SW_PUSH_6mm` |
| SW2 | BOOT Button | Same as SW1 |
| SW3 | ACTION Button | Same as SW1 (or larger if user-facing) |
| SW4 | Power Switch (SPDT) | Depends on chosen slide switch. Common: `Button_Switch_SMD:SW_SPDT_CK_JS102011SAQN` |
| SW5 | Mode Switch (SPDT) | Same as SW4 |
| J1 | UART Header (4-pin) | `Connector_PinHeader_2.54mm:PinHeader_1x04_P2.54mm_Vertical` |
| J2 | TM1637 Connector (4-pin) | `Connector_PinHeader_2.54mm:PinHeader_1x04_P2.54mm_Vertical` |
| J3 | TP4056 Module (6-pin) | `Connector_PinHeader_2.54mm:PinHeader_1x06_P2.54mm_Vertical` |
| J4 | HB100 Radar (3-pin) | `Connector_PinHeader_2.54mm:PinHeader_1x03_P2.54mm_Vertical` |
| H1-H4 | M3 Mounting Holes | `MountingHole:MountingHole_3.2mm_M3` |

---

## Order of Operations

1. ✏️ Fix R16, R17 values
2. 🔍 Verify C12/C13 net and Q1 orientation
3. ➕ (Optional) Add pull-ups and test points
4. ✅ Run ERC
5. 📦 Assign all remaining footprints
6. 🔄 Generate netlist / Update PCB from schematic
7. 🏗️ Start PCB layout
