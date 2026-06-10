# Pocket Radar Gun Project

## Project Architecture
1. **Phase 1: Offline Hardware Device** (PCB, Sensor, Display)
2. **Phase 2: Cloud Integration** (Wi-Fi data logging)
3. **Phase 3: Web Dashboard** (Player analytics)

## Component Bill of Materials (BOM)
- **Microcontroller**: ESP32-S3-WROOM-1 (Bare module, surface mount)
  - 1x 10µF, 1x 0.1µF Ceramic Capacitors (Decoupling)
  - 2x 10kΩ Resistors (Pull-ups for EN and BOOT)
  - 1x 1µF Capacitor (EN delay)
  - 2x Tactile Push Buttons (BOOT and RESET)
  - 1x 4-pin Male Header (UART Programming)
  - 1x Status LED (Blue) + Current-limiting Resistor (see Known Issues)
- **Sensor**: HB100 Microwave Doppler Sensor (10.525 GHz)
  - 1x 10µF, 1x 0.1µF Ceramic Capacitors (Noise Decoupling at VCC)
- **Analog Front-End**: LM358 Op-Amp (2-stage active band-pass filter, ~10000x gain, 2.5V virtual ground)
  - 3x 100kΩ Resistors (Virtual ground and ADC bias)
  - 2x 10kΩ Resistors (Input stage)
  - 2x 1MΩ Resistors (Feedback gain)
  - 2x 47pF Capacitors (Low-pass filter)
  - 2x 1µF Capacitors (AC coupling / High-pass)
  - 2x 10µF Capacitors (Virtual ground decoupling & ADC AC coupling)
- **Power Management**:
  - 3.7V Li-Po Battery
  - TP4056 Module (Pre-made module with USB-C, TP4056 + DW01A protection)
  - 1x SPDT Slide Switch (Main Power - `SW_Power`)
  - MT3608 Boost Converter (Bare IC)
    - 1x 22µH Inductor, 1x SS34 Schottky Diode
    - Feedback Resistors: R1 = 73.2kΩ (top), R2 = 10kΩ (bottom) → 5V output
    - 2x 10µF Capacitors (Input & Output)
  - AP2112K-3.3 Ultra-Low Dropout Regulator (3.3V for ESP32)
    - 2x 1µF Capacitors (Input and Output Decoupling)
  - AO3401A P-Channel MOSFET (High-side switching to power down HB100)
  - 2N7002 N-Channel MOSFET + 10kΩ Pull-up Resistor (Level shifter for AO3401)
  - Voltage divider (2x 100kΩ) for battery level sensing
  - 1x Power LED (Red) + Current-limiting Resistor (see Known Issues)
- **User Interface**: TM1637 4-digit 7-segment LED display, tactile action button
  - 1x SPDT Slide Switch (Mode Selection - `SW_Mode`)
- **Miscellaneous**: 
  - 4x M3 Mounting Holes

## KiCad Schematic Reference Designators
| Ref | Component | Value | Footprint Status |
|-----|-----------|-------|------------------|
| U1 | ESP32-S3-WROOM-1 | — | ✅ Assigned (PCM_Espressif) |
| U2 | AP2112K-3.3 (LDO) | 3.3V | ✅ SOT-23-5 |
| U3 | MT3608 (Boost) | 5V | ✅ SOT-23-6 |
| U4 | LM358 (Op-Amp) | — | ✅ SOIC-8 |
| Q1 | AO3401A (P-FET) | — | ✅ SOT-23 |
| Q2 | 2N7002 (N-FET) | — | ✅ SOT-23 |
| D1 | SS34 (Schottky) | — | ✅ D_SMA |
| D2 | Power LED (Red) | — | ✅ LED_SMD:LED_0805_2012Metric (HandSolder) |
| D3 | Status LED (Blue) | — | ✅ LED_SMD:LED_0805_2012Metric (HandSolder) |
| R1 | MT3608 FB top | 73.2kΩ | ✅ 0805 |
| R2 | MT3608 FB bottom | 10kΩ | ✅ 0805 |
| R3 | AO3401 gate pull-up | 10kΩ | ✅ 0805 |
| R4 | BAT sense top | 100kΩ | ✅ 0805 |
| R5 | BAT sense bottom | 100kΩ | ✅ 0805 |
| R6 | EN pull-up | 10kΩ | ✅ 0805 |
| R7 | BOOT pull-up | 10kΩ | ✅ 0805 |
| R8 | V_REF divider top | 100kΩ | ✅ 0805 |
| R9 | V_REF divider bottom | 100kΩ | ✅ 0805 |
| R10 | AFE Stage 1 input | 10kΩ | ✅ 0805 |
| R11 | AFE Stage 1 feedback | 1MΩ | ✅ 0805 |
| R12 | AFE Stage 2 input | 10kΩ | ✅ 0805 |
| R13 | AFE Stage 2 feedback | 1MΩ | ✅ 0805 |
| R14 | ADC bias top | 100kΩ | ✅ 0805 |
| R15 | ADC bias bottom | 100kΩ | ✅ 0805 |
| R16 | Power LED resistor | 470Ω | ✅ 0805 (VALUE NEEDS CHANGE) |
| R17 | Status LED resistor | 220Ω | ✅ 0805 (VALUE NEEDS CHANGE) |
| C1 | AP2112 input decouple | 1µF | ✅ 0805 |
| C2 | AP2112 output decouple | 1µF | ✅ 0805 |
| C3 | ESP32 decouple (bulk) | 10µF | ✅ 0805 |
| C4 | ESP32 decouple (HF) | 0.1µF | ✅ 0805 |
| C5 | EN delay cap | 1µF | ✅ 0805 |
| C6 | V_REF decouple | 10µF | ✅ 0805 |
| C7 | AFE Stage 1 AC coupling | 1µF | ✅ 0805 |
| C8 | AFE Stage 1 low-pass | 47pF | ✅ 0805 |
| C9 | AFE Stage 2 AC coupling | 1µF | ✅ 0805 |
| C10 | AFE Stage 2 low-pass | 47pF | ✅ 0805 |
| C11 | ADC output AC coupling | 10µF | ✅ 0805 |
| C12 | HB100 VCC decouple (bulk) | 10µF | ✅ 0805 |
| C13 | HB100 VCC decouple (HF) | 0.1µF | ✅ 0805 |
| L1 | MT3608 inductor | 22µH | ✅ Inductor_SMD:L_1210_3225Metric (HandSolder) |
| BT1 | Li-Po Battery | 3.7V | ✅ Connector_JST:JST_PH_B2B-PH-K_1x02_P2.00mm_Vertical |
| SW1 | RESET button | — | ✅ Button_Switch_THT:SW_PUSH_6mm |
| SW2 | BOOT button | — | ✅ Button_Switch_THT:SW_PUSH_6mm |
| SW3 | ACTION button | — | ✅ Button_Switch_THT:SW_PUSH_6mm |
| SW4 | Main Power Switch (SPDT) | — | ✅ Button_Switch_THT:SW_Slide_SPDT_SS12D00 |
| SW5 | Mode Select Switch (SPDT) | — | ✅ Button_Switch_THT:SW_Slide_SPDT_SS12D00 |
| J1 | UART Programming header | 4-pin | ✅ Connector_PinHeader_2.54mm:PinHeader_1x04_P2.54mm_Vertical |
| J2 | TM1637 Display connector | 4-pin | ✅ Connector_PinHeader_2.54mm:PinHeader_1x04_P2.54mm_Vertical |
| J3 | TP4056 Module connector | 6-pin | ✅ Connector_PinHeader_2.54mm:PinHeader_1x06_P2.54mm_Vertical |
| J4 | HB100 Radar connector | 3-pin | ✅ Connector_PinHeader_2.54mm:PinHeader_1x03_P2.54mm_Vertical |
| H1–H4 | Mounting Holes | M3 | ✅ MountingHole:MountingHole_3.2mm_M3 |

## Pinout Mappings
### Power Management Block
- **TP4056 Module**: `B+`/`B-` to Battery, `OUT+` to `SW_Power` Pin 1, `OUT-` to system `GND`.
- **Main Power Switch**: `SW_Power` Pin 2 (Common) to `BAT+` net. Pin 3 is No Connect.
- **AP2112K-3.3**: `VIN` & `EN` to `BAT+`, `GND` to `GND`, `VOUT` to `3V3`.
- **MT3608**: `VIN` & `EN` to `BAT+`, `GND` to `GND`, Output tuned to `5V_BOOST`.
  - Feedback divider: `5V_BOOST` → R1 (73.2kΩ) → FB pin → R2 (10kΩ) → GND. Gives VOUT = 0.6V × (1 + 73.2k/10k) ≈ 5.0V.
- **HB100 Switch**:
  - AO3401A (P-Ch): Source = `5V_BOOST`, Drain = `HB100_VCC`, Gate = Pull-up to `5V_BOOST` & 2N7002 Drain.
  - 2N7002 (N-Ch): Source = `GND`, Gate = ESP32 `HB100_EN` pin.
- **Battery Sense**: `BAT+` -> 100kΩ -> ESP32 `BAT_SENSE_ADC` -> 100kΩ -> `GND`.

### ESP32 Core Block
- **Power**: `3V3` to `3V3` pin, `GND` to all `GND` pins. Decoupling: 10µF + 0.1µF.
- **Reset (EN)**: `EN` pin pulled up to `3V3` via 10kΩ, 1µF to `GND`, Reset Button to `GND`.
- **Boot (GPIO0)**: `IO0` pin pulled up to `3V3` via 10kΩ, Boot Button to `GND`.
- **Programming (UART)**: 4-pin header (`GND`, `3V3`, `TX_PROG` to `IO43` / `TXD0`, `RX_PROG` to `IO44` / `RXD0`).
- **Peripherals**:
  - `STATUS_LED`: `IO46` -> Resistor (R17) -> Blue LED -> `GND`
  - `MODE_SEL`: `IO10` (Mode selection switch input, uses internal pull-up)
  - `HB100_ADC`: `IO6` (ADC1_CH5)
  - `BAT_SENSE_ADC`: `IO7` (ADC1_CH6)
  - `HB100_EN`: `IO8`
  - `TM1637_CLK`: `IO4`
  - `TM1637_DIO`: `IO5`
  - `ACTION_BTN`: `IO9` (Action button input, uses internal pull-up)

### Analog Front-End (LM358)
- **Power**: Pin 8 (`VCC`) to `5V_BOOST`, Pin 4 (`GND`) to `GND`.
- **Virtual Ground (V_REF)**: 2x 100kΩ voltage divider from `5V_BOOST` to `GND`, 10µF to `GND`.
- **Stage 1**: HB100 `IF` -> 1µF -> 10kΩ -> Pin 2. `V_REF` to Pin 3. 1MΩ and 47pF from Pin 1 to Pin 2.
- **Stage 2**: Pin 1 -> 1µF -> 10kΩ -> Pin 6. `V_REF` to Pin 5. 1MΩ and 47pF from Pin 7 to Pin 6.
- **ADC Output**: Pin 7 -> 10µF -> `HB100_ADC` net. 100kΩ from `HB100_ADC` to `3V3`, 100kΩ to `GND`.

### User Interface Block
- **TM1637 Display**: `VCC` to `3V3`, `GND` to `GND`, `CLK` to `TM1637_CLK` (IO4), `DIO` to `TM1637_DIO` (IO5).
- **Action Button**: One side to `ACTION_BTN` (IO9), other side to `GND`.
- **Mode Switch**: Pin 2 (Common) to `MODE_SEL` (IO10), Pin 1 to `GND`, Pin 3 to No Connect.

## Known Issues & Required Schematic Changes
These were identified during a full schematic audit and MUST be addressed before finalizing the PCB layout.

### MUST FIX — LED Current-Limiting Resistors
- **R17 (Blue Status LED)**: Currently 1kΩ. Blue LEDs have ~3.0-3.2V forward voltage. At 3.3V GPIO: I = (3.3 - 3.0) / 1000 = 0.3mA → nearly invisible. **Change R17 to 100Ω–220Ω** for ~1-1.5mA (visible for high-efficiency LED).
- **R16 (Red Power LED)**: Currently 1kΩ. Red LEDs have ~2.0V Vf. At 3.3V: I = 1.3mA → dim but functional. **Consider changing R16 to 330Ω–470Ω** for 2.8-3.9mA if brighter indicator is desired.

### SHOULD VERIFY — HB100 Decoupling Caps Placement
- C12 (10µF) and C13 (0.1µF) must be on the **switched** `HB100_VCC` net (after the AO3401A MOSFET), NOT on the `5V_BOOST` rail before the switch. Verify in schematic that they connect to the correct net. On PCB, place these as physically close to the HB100 connector (J4) as possible.

### RECOMMENDED — External Pull-Up Resistors on Button/Switch Inputs
- `ACTION_BTN` (IO9) and `MODE_SEL` (IO10) currently rely on ESP32 internal pull-ups (~45kΩ). The HB100 sensor emits 10.525 GHz which can couple noise into nearby traces. Consider adding **10kΩ external pull-up resistors** to `3V3` on IO9 and IO10 for better noise immunity.

### RECOMMENDED — AO3401A Source/Drain Orientation
- Visually verify in KiCad that Q1 (AO3401A) has: Source → `5V_BOOST`, Drain → `HB100_VCC`. The KiCad symbol pinout is: Pin 1 = Gate, Pin 2 = Source, Pin 3 = Drain.

## Current Task Status
- Phase 1: Schematic Capture in KiCad is **MOSTLY COMPLETE** — needs the fixes listed in "Known Issues" above.
- Full schematic audit has been performed. Circuit topology and component values are verified correct (MT3608 feedback divider, battery sense divider, LM358 filter stages, ESP32 boot/reset circuitry).
- **All footprints are now ASSIGNED** — all 27 components have footprints in the schematic. PCB layout can begin.

## Outstanding Issues
1. Fix LED resistor values (R16, R17) in schematic — change to 470Ω (Red) and 220Ω (Blue).
2. Verify HB100 decoupling cap net placement (C12, C13).
3. (Optional) Add external pull-ups on IO9, IO10.
4. Run KiCad ERC (Electrical Rules Check) to confirm no errors.
5. Begin PCB Layout: define board outline, place components, route traces.

## PCB Layout Guidelines (For When Layout Begins)
- **Keep HB100 analog signal path short**: Place LM358 (U4) and its surrounding passives close to the HB100 connector (J4). Keep `HB100_IF` and `HB100_ADC` traces short and away from digital signals.
- **Star ground or ground plane**: Use a solid ground plane on one layer. Keep analog ground and digital ground meeting at a single point if possible.
- **MT3608 layout is critical**: Keep L1, D1, and input/output caps (C1, C3) physically close to U3. The SW pin trace should be short and wide. Route the feedback divider (R1, R2) close to the FB pin, away from the inductor.
- **Decoupling caps close to IC pins**: All decoupling capacitors should be placed as close as possible to their respective IC power pins.
- **Antenna keep-out**: The ESP32-S3-WROOM-1 module has an on-board PCB antenna. Keep a ground-plane clear zone under and around the antenna area per the Espressif datasheet recommendations.
- **Battery connector and power switch**: Place near the board edge for easy access.
- **Board size target**: Keep compact for handheld use — aim for ~60-80mm × 40-50mm.
