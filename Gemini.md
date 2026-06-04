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
  - 1x Status LED (Blue) + 1kΩ Resistor
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
    - 2x 10µF Capacitors, Feedback Resistors
  - AP2112K-3.3 or ME6211 Ultra-Low Dropout Regulator (3.3V for ESP32)
    - 2x 1µF Capacitors (Input and Output Decoupling)
  - AO3401 P-Channel MOSFET (High-side switching to power down HB100)
  - 2N7002 N-Channel MOSFET + 10kΩ Pull-up Resistor (Level shifter for AO3401)
  - Voltage divider (2x 100kΩ) for battery level sensing
  - 1x Power LED (Red) + 1kΩ Resistor
- **User Interface**: TM1637 4-digit 7-segment LED display, tactile action button
  - 1x SPDT Slide Switch (Mode Selection - `SW_Mode`)
- **Miscellaneous**: 
  - 4x M3 Mounting Holes

## Pinout Mappings
### Power Management Block
- **TP4056 Module**: `B+`/`B-` to Battery, `OUT+` to `SW_Power` Pin 1, `OUT-` to system `GND`.
- **Main Power Switch**: `SW_Power` Pin 2 (Common) to `BAT+` net. Pin 3 is No Connect.
- **AP2112K-3.3**: `VIN` & `EN` to `BAT+`, `GND` to `GND`, `VOUT` to `3V3`.
- **MT3608**: `VIN` & `EN` to `BAT+`, `GND` to `GND`, Output tuned to `5V_BOOST`.
- **HB100 Switch**:
  - AO3401 (P-Ch): Source = `5V_BOOST`, Drain = `HB100_VCC`, Gate = Pull-up to `5V_BOOST` & 2N7002 Drain.
  - 2N7002 (N-Ch): Source = `GND`, Gate = ESP32 `HB100_EN` pin.
- **Battery Sense**: `BAT+` -> 100kΩ -> ESP32 `BAT_SENSE_ADC` -> 100kΩ -> `GND`.

### ESP32 Core Block
- **Power**: `3V3` to `3V3` pin, `GND` to all `GND` pins. Decoupling: 10µF + 0.1µF.
- **Reset (EN)**: `EN` pin pulled up to `3V3` via 10kΩ, 1µF to `GND`, Reset Button to `GND`.
- **Boot (GPIO0)**: `IO0` pin pulled up to `3V3` via 10kΩ, Boot Button to `GND`.
- **Programming (UART)**: 4-pin header (`GND`, `3V3`, `TX_PROG` to `IO43` / `TXD0`, `RX_PROG` to `IO44` / `RXD0`).
- **Peripherals**:
  - `STATUS_LED`: `IO46` -> 1kΩ Resistor -> Blue LED -> `GND`
  - `MODE_SEL`: `IO10` (Mode selection switch input)
  - `HB100_ADC`: `IO6` (ADC1_CH5)
  - `BAT_SENSE_ADC`: `IO7` (ADC1_CH6)
  - `HB100_EN`: `IO8`
  - `TM1637_CLK`: `IO4`
  - `TM1637_DIO`: `IO5`
  - `ACTION_BTN`: `IO9`

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

## Current Task Status
- Phase 1: Schematic Capture in KiCad is **COMPLETE**.
- All schematic blocks (Power, ESP32, Analog Front-End, User Interface) are finalized, double-checked, and documented.

## Outstanding Issues
- Next Phase: PCB Layout. Need to assign footprints, place components, and route the board in KiCad.
