# Radargun PCB Layout Guide — Step by Step

This guide walks you through the PCB layout process in KiCad, from board outline to final DRC check. Follow the steps in order.

---

## Step 1: Board Setup & Design Rules

Before placing anything, configure your design rules in KiCad:

### Open Board Setup
**File → Board Setup** (or the gear icon), then set:

| Setting | Value | Why |
|---|---|---|
| Board thickness | 1.6mm | Standard, cheapest to manufacture |
| Copper layers | 2 (F.Cu + B.Cu) | Sufficient for this design |
| Min track width | 0.2mm | Safe minimum for most fabs (JLCPCB, PCBWay) |
| Min clearance | 0.2mm | Safe minimum |
| Min via drill | 0.3mm | Standard |
| Via outer diameter | 0.6mm–0.8mm | Standard |
| Default track width | 0.25mm | For signal traces |

### Add Track Width Presets
Under **Board Setup → Design Rules → Net Classes** or **Pre-defined Sizes**, add:
- **0.25mm** — Signal traces (default)
- **0.5mm** — 3.3V power, battery sense
- **0.8mm** — BAT+, 5V_BOOST main power traces
- **1.0mm** — GND bus (where not using ground plane)

---

## Step 2: Draw the Board Outline

1. Select the **Edge.Cuts** layer (yellow, in the layer selector on the right)
2. Use **Place → Line** (or the draw line tool) to draw a rectangle
3. **Recommended starting size**: 70mm × 45mm (you can adjust later)
4. For rounded corners, use **Place → Arc** on Edge.Cuts
5. Leave ~3mm from each corner for mounting holes

> **Tip**: If you have a 3D-printed case already designed, match its internal dimensions.

---

## Step 3: Component Placement

This is the most important step. A good placement makes routing 10x easier. **Place in this order:**

### 3.1 — ESP32-S3-WROOM-1 (U1) — Place FIRST
- Place it near the **top edge** of the board, with the **antenna hanging over the board edge** (or flush with it)
- The antenna is at the top of the module (the end without castellated pins)
- Orient it so the programming pins (IO43/TX, IO44/RX) face toward where J1 (UART header) will be

```
  ┌──────────────────────────────────────┐
  │  [ANTENNA OVERHANG — NO COPPER HERE] │
  │  ┌──────────────┐                    │
  │  │              │                    │
  │  │    ESP32     │                    │
  │  │     U1       │                    │
  │  │              │                    │
  │  └──────────────┘                    │
  │                                      │
  │                                      │
  └──────────────────────────────────────┘
```

### 3.2 — ESP32 Support Components
Place these **immediately adjacent** to U1:
- **C3 (10µF)** + **C4 (0.1µF)**: Decoupling caps → right next to the 3V3 and GND pins of U1
- **R6 (10kΩ)** + **C5 (1µF)**: EN pull-up and delay cap → next to the EN pin
- **R7 (10kΩ)**: BOOT pull-up → next to the IO0/BOOT pin
- **SW1 (RESET)** + **SW2 (BOOT)**: Near the EN and IO0 pins, accessible from board edge

### 3.3 — Power Management Cluster
Place the power section on the **left side** or **bottom-left** of the board:

#### MT3608 Boost Converter (U3) cluster — CRITICAL LAYOUT
```
     ┌────────────┐
     │   MT3608   │
     │    U3      │
     └────────────┘
  L1 (22µH)    D1 (SS34)
  ↕              ↕
  [Input Cap]  [Output Cap]
  R1, R2 (FB divider) → close to FB pin
```
- L1 must be **right next to** the SW pin of U3
- D1 must be **right next to** L1's other terminal and the output
- R1 (73.2kΩ) and R2 (10kΩ) feedback divider → close to the FB pin, **away from the inductor** (inductors radiate magnetic field)
- Input/output caps → as close as possible to VIN and VOUT

#### AP2112K LDO (U2)
- Place near U1 (ESP32) since it supplies 3.3V to it
- C1 (input) and C2 (output) → right at the VIN and VOUT pins

#### Battery & Power Switch
- **BT1 (JST connector)**: Board edge, easy to plug in
- **SW4 (Power switch)**: Board edge, accessible
- **R4, R5 (battery sense divider)**: Near the ESP32 ADC pin (IO7)

### 3.4 — HB100 Power Switch (Q1, Q2, R3)
- Place between the 5V_BOOST rail and J4 (HB100 connector)
- Q1 (AO3401A), Q2 (2N7002), R3 (gate pull-up) form a small cluster

### 3.5 — Analog Front-End (LM358)
Place on the **opposite side** of the board from the MT3608 boost converter:

```
  J4 (HB100) → [C12,C13] → [Q1 switch] → ...
  
  HB100_IF signal path:
  J4 pin → C7 → R10 → U4 pin2 → [R11||C8 feedback] → U4 pin1
         → C9 → R12 → U4 pin6 → [R13||C10 feedback] → U4 pin7
         → C11 → R14/R15 bias → HB100_ADC to ESP32
```

- **J4 (HB100 connector)**: Board edge
- **C12, C13**: Right next to J4
- **U4 (LM358)**: Close to J4
- **R8, R9, C6**: V_REF voltage divider → near U4
- **Stage 1 passives** (C7, R10, R11, C8): Clustered around U4 pins 1-3
- **Stage 2 passives** (C9, R12, R13, C10): Clustered around U4 pins 5-7
- **Output (C11, R14, R15)**: Between U4 output and ESP32 ADC pin

> ⚠️ Keep the entire analog section AWAY from the MT3608 switching converter. The boost converter switches at hundreds of kHz and will inject noise into sensitive analog traces.

### 3.6 — User Interface Components
- **J2 (TM1637 display connector)**: Board edge, wherever your display faces
- **SW3 (Action button)**: Board edge or panel-mount
- **SW5 (Mode switch)**: Board edge
- **D2 (Power LED)** + **R16**: Visible location near power switch
- **D3 (Status LED)** + **R17**: Visible location

### 3.7 — Remaining
- **J1 (UART header)**: Board edge, near ESP32 TX/RX pins
- **J3 (TP4056 module connector)**: Board edge, near battery connector
- **H1–H4 (Mounting holes)**: Four corners, ~3mm from edges

---

## Step 4: Routing

### 4.1 — Set Up Ground Plane FIRST
Before routing any traces:
1. Select **B.Cu** (back copper layer)
2. **Place → Add Copper Zone** (or the zone tool)
3. Click around the entire board outline
4. Set the net to **GND**
5. Set thermal relief to **Thermal Relief** (not solid — easier to solder)
6. Click **Edit → Fill All Zones** (or press **B**)

This gives you a solid ground plane on the back of the board. Now you only need to route the signal and power traces on the front.

### 4.2 — Route Power Traces First
Use wider traces (0.5mm–1.0mm):
1. **BAT+ rail**: From SW4 → U3 VIN, U2 VIN, R4 (battery sense)
2. **5V_BOOST rail**: From MT3608 output → Q1 source, R8 (V_REF top), U4 VCC (pin 8)
3. **3.3V rail**: From U2 VOUT → U1 (ESP32 3V3), J2 (TM1637 VCC), R14 (ADC bias), pull-up resistors
4. **GND connections**: Add vias to the back-side ground plane near every IC GND pin

### 4.3 — Route Analog Signals
Use 0.25mm traces, keep them short and direct:
1. **HB100_IF**: J4 → C7 → R10 → U4 pin 2
2. **Stage 1 output**: U4 pin 1 → C9 → R12 → U4 pin 6
3. **Stage 2 output**: U4 pin 7 → C11 → R14/R15 junction → ESP32 IO6
4. **V_REF**: R8/R9 junction → U4 pin 3 and pin 5

> ⚠️ Do NOT route analog traces near or parallel to the MT3608's SW trace, L1, or any switching signals.

### 4.4 — Route Digital Signals
Use 0.25mm traces:
1. **TM1637_CLK** (IO4) and **TM1637_DIO** (IO5): To J2
2. **HB100_EN** (IO8): To Q2 gate
3. **ACTION_BTN** (IO9): To SW3
4. **MODE_SEL** (IO10): To SW5
5. **STATUS_LED** (IO46): To R17 → D3
6. **TX_PROG / RX_PROG** (IO43/IO44): To J1

### 4.5 — Add Stitching Vias
Add extra GND vias around the board to connect F.Cu ground pads/traces to the B.Cu ground plane:
- Near every IC
- Between the analog and digital sections (acts as a shield)
- Around the board perimeter

---

## Step 5: Copper Zones (Front Side)

Optionally, add a ground fill on F.Cu as well:
1. **Place → Add Copper Zone** on F.Cu
2. Set net to **GND**
3. Draw around the board outline
4. **IMPORTANT**: Add a **keep-out zone** under the ESP32 antenna area (no copper on F.Cu or B.Cu there)
5. Fill all zones (**B** key)

---

## Step 6: Silkscreen & Labeling

Add helpful text on the **F.SilkS** layer:
- Board name: "RADARGUN v1.0"
- Your name / date
- Label connectors: "BATT", "UART", "HB100", "DISPLAY"
- Label switches: "PWR", "MODE", "RST", "BOOT", "ACTION"
- Polarity markers on BT1 (+/-)

---

## Step 7: Design Rules Check (DRC)

1. **Inspect → Design Rules Checker**
2. Click **Run DRC**
3. Fix ALL errors (red). Common issues:
   - Unrouted nets (forgotten connections)
   - Clearance violations (traces too close)
   - Track width violations
4. Warnings (yellow) can usually be ignored but review them

---

## Step 8: 3D Preview & Final Check

1. **View → 3D Viewer** (Alt+3)
2. Check that:
   - All components are on the correct side
   - No components overlap
   - Connectors face the right direction for your enclosure
   - ESP32 antenna area is clear
   - Everything fits within your case dimensions

---

## Common Mistakes to Avoid

| Mistake | How to Avoid |
|---|---|
| Copper under ESP32 antenna | Add a keep-out zone on BOTH layers under the antenna |
| MT3608 switching loop too large | Keep L1, D1, and caps physically touching U3 |
| Analog traces near boost converter | Place the LM358 section on the opposite side of the board |
| No ground plane | Always fill B.Cu with GND copper zone |
| Decoupling caps far from IC pins | Place caps within 2-3mm of the power pins |
| Thin power traces | Use 0.5mm+ for all power rails |
| Forgetting vias to ground plane | Add GND vias near every IC and between board sections |
| Components too close to board edge | Keep clearance ≥1mm from Edge.Cuts (fab requirement) |
| Mounting holes interfering with traces | Add keep-out zones around mounting holes |

---

## Quick Reference: KiCad Hotkeys for PCB Layout

| Key | Action |
|---|---|
| **X** | Route trace |
| **V** | Add via (while routing) |
| **B** | Fill all copper zones |
| **D** | Start interactive router |
| **U** | Select entire track |
| **E** | Edit properties of selected item |
| **R** | Rotate component |
| **F** | Flip component to other side |
| **M** | Move component |
| **Ctrl+Z** | Undo |
| **Space** | Reset rotation origin |
| **+/-** | Change layer |
| **Esc** | Cancel current action |
