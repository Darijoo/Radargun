# Radargun Bill of Materials (BOM) & Order List

Here is your comprehensive order list for all components required to build the Radargun PCB and assemble the device. When ordering from suppliers like DigiKey, Mouser, LCSC, or AliExpress, use the parameters provided to ensure you get the right parts.

---

## 🧠 Core Modules & Sensors

| Qty | Component / Module | Description & Specifications | Sourcing Notes |
|:---:|---|---|---|
| **1** | **ESP32-S3-WROOM-1** | ESP32-S3 module, PCB antenna | Order the bare module for SMD soldering (e.g., `ESP32-S3-WROOM-1-N8` for 8MB Flash). | https://nl.aliexpress.com/item/1005008168727665.html?
| **1** | **HB100 Sensor** | 10.525 GHz Microwave Doppler Radar | Pre-made module. Common on Amazon/AliExpress. | https://nl.aliexpress.com/item/1005006236185737.html?
| **1** | **TM1637 Display** | 4-digit 7-segment display module | Get the version with a 4-pin male header (VCC, GND, DIO, CLK). |  https://nl.aliexpress.com/item/1005009533582442.html?
| **1** | **TP4056 Module** | USB-C Li-Po Charger + Protection | Standard blue/black pre-made module with USB-C and 6 pads (IN+, IN-, B+, B-, OUT+, OUT-). | https://nl.aliexpress.com/item/1005012104722935.html?
| **1** | **Li-Po Battery** | 3.7V single-cell Lithium Polymer | Choose a capacity that fits your enclosure (e.g., 500mAh to 1200mAh). Must have a JST-PH 2.0mm connector. | https://nl.aliexpress.com/item/1005012334689616.html?

---

## 🔌 Integrated Circuits (ICs) & Active Components

| Qty | Component | Ref | Package | Specifications | Sourcing Notes |
|:---:|---|---|---|---|---|
| **1** | **MT3608** | U3 | SOT-23-6 | Step-up Boost Converter IC | Standard bare IC. | https://nl.aliexpress.com/item/1005006361814667.html?
| **1** | **AP2112K-3.3** | U2 | SOT-23-5 | 3.3V Ultra-Low Dropout (LDO) | Max input 6V, output 3.3V, 600mA. | https://nl.aliexpress.com/item/1005008069741507.html?
| **1** | **LM358** | U4 | SOIC-8 | Dual Operational Amplifier | Very common (e.g., LM358DR). | https://nl.aliexpress.com/item/1005006217085876.html?
| **1** | **AO3401A** | Q1 | SOT-23 | P-Channel MOSFET | VDS= -30V, ID= -4A. Used for high-side switching. | https://nl.aliexpress.com/item/1005006220143887.html?
| **1** | **2N7002** | Q2 | SOT-23 | N-Channel MOSFET | VDS= 60V, Logic-level gate. | https://nl.aliexpress.com/item/1005006109969648.html?
| **1** | **SS34** | D1 | SMA (DO-214AC) | Schottky Diode | 3A, 40V. Required for the MT3608 boost circuit. | https://nl.aliexpress.com/item/1005009947560623.html?
---

## 🎛️ Electro-Mechanical (Switches, Buttons, LEDs)

| Qty | Component | Ref | Package | Sourcing Notes |
|:---:|---|---|---|---|
| **3** | **Tactile Push Button** | SW1, SW2, SW3 | 6x6mm THT or SMD | Used for RESET, BOOT, and the ACTION trigger. If the action button is on the case, get a panel-mount button and run wires to the PCB instead. | https://nl.aliexpress.com/item/1005006066621804.html?
| **2** | **Slide Switch (SPDT)**| SW4, SW5 | Breadboard pitch or panel mount | Used for Main Power and Mode Selection. Check dimensions so it fits your 3D case. | https://nl.aliexpress.com/item/1005012369785017.html?
| **1** | **Red LED** | D2 | 0805 SMD | Power indicator. | https://nl.aliexpress.com/item/1005005226854182.html?
| **1** | **Blue LED** | D3 | 0805 SMD | Status indicator. | https://nl.aliexpress.com/item/1005005226854182.html?
| **1** | **JST-PH Connector** | BT1 | 2-pin, 2.0mm pitch | Vertical or Right-Angle connector for the battery. | https://nl.aliexpress.com/item/1005003431613901.html?
| **~13** | **Pin Headers** | J1, J2, J3, J4 | 2.54mm pitch (0.1") | Standard male/female 2.54mm headers for UART (4), TM1637 (4), TP4056 (6, optional if soldering directly), and HB100 (3). | 

---

## ⚡ Passive Components (Resistors & Capacitors)
*All passives are **0805 SMD package** unless otherwise noted.*

### Resistors (0805, 1% tolerance recommended)
| Qty | Value | Ref | Purpose |
|:---:|---|---|---|
| **1** | **73.2 kΩ** | R1 | MT3608 Feedback divider top | https://nl.aliexpress.com/item/1005008715919041.html
| **6** | **10 kΩ** | R2, R3, R6, R7, R10, R12 | Pull-ups, Op-Amp inputs, MT3608 Feedback | https://nl.aliexpress.com/item/1005008715919041.html
| **6** | **100 kΩ** | R4, R5, R8, R9, R14, R15 | Voltage dividers (Battery, V_REF, ADC Bias) | https://nl.aliexpress.com/item/1005008715919041.html
| **2** | **1 MΩ** | R11, R13 | Op-Amp feedback gain | https://nl.aliexpress.com/item/1005008715919041.html
| **1** | **470 Ω** | R16 | Power LED current limiter | https://nl.aliexpress.com/item/1005008715919041.html
| **1** | **220 Ω** | R17 | Status LED current limiter | https://nl.aliexpress.com/item/1005008715919041.html
*(Note: I added 2 extra 10kΩ resistors for the optional IO9/IO10 pull-ups mentioned earlier, so you have plenty).*

### Capacitors (0805, Ceramic MLCC)
| Qty | Value | Ref | Voltage / Details |
|:---:|---|---|---|
| **5** | **10 µF** | C3, C6, C11, C12, C? (Boost) | 16V rating or higher. (Decoupling and AC coupling) | https://nl.aliexpress.com/item/1005007476892288.html?
| **5** | **1 µF** | C1, C2, C5, C7, C9 | 16V rating or higher. (LDO decoupling, EN delay, AC coupling) | https://nl.aliexpress.com/item/1005007476892288.html?
| **3** | **0.1 µF (100nF)**| C4, C13, etc. | 16V rating or higher. (High-frequency decoupling) | https://nl.aliexpress.com/item/1005007476892288.html?
| **2** | **47 pF** | C8, C10 | 50V rating. C0G/NP0 dielectric preferred for Op-Amp filter. | https://nl.aliexpress.com/item/1005007476892288.html?

### Inductors
| Qty | Component | Ref | Package | Details |
|:---:|---|---|---|---|
| **1** | **22 µH** | L1 | SMD (e.g., 1210 or 1212) | Power inductor. Saturation current (Isat) > 1.5A. Usually ~3.2x3.2mm or 4x4mm wire-wound SMD. | https://nl.aliexpress.com/item/1005003018050902.html

---

## 🛠️ Summary & Tips for Ordering
1. **Passives (Resistors/Caps):** It is usually cheaper to buy a "sample book" or "assortment kit" of 0805 resistors and capacitors from Amazon/AliExpress than to buy individual values, plus you'll have spares for future projects.
2. **Modules vs Components:** Make sure you order the bare ICs for the AP2112, MT3608, LM358, and MOSFETs, but get the pre-assembled modules for the TP4056 and TM1637.
3. **Buttons/Switches:** Measure your intended 3D printed enclosure first! If your buttons need to stick out of the case, you may want panel-mounted buttons wired to the PCB rather than soldering them directly to the board.
