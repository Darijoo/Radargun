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
- **Sensor**: HB100 Microwave Doppler Sensor (10.525 GHz)
- **Analog Front-End**: LM358 Op-Amp (2-stage active band-pass filter, ~10000x gain, 2.5V virtual ground)
- **Power Management**:
  - 3.7V Li-Po Battery
  - TP4056 Module (Pre-made module with USB-C, TP4056 + DW01A protection)
  - MT3608 Boost Converter (Stepping up to 5V for HB100)
  - AP2112K-3.3 or ME6211 Ultra-Low Dropout Regulator (3.3V for ESP32)
  - AO3401 P-Channel MOSFET (High-side switching to power down HB100)
  - 2N7002 N-Channel MOSFET + 10kΩ Pull-up Resistor (Level shifter for AO3401)
  - Voltage divider (2x 100kΩ) for battery level sensing
- **User Interface**: TM1637 4-digit 7-segment LED display, tactile action button

## Pinout Mappings
### Power Management Block
- **TP4056 Module**: `B+`/`B-` to Battery, `OUT+` to system `BAT+`, `OUT-` to system `GND`.
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
  - `HB100_ADC`: `IO6` (ADC1_CH5)
  - `BAT_SENSE_ADC`: `IO7` (ADC1_CH6)
  - `HB100_EN`: `IO8`
  - `TM1637_CLK`: `IO4`
  - `TM1637_DIO`: `IO5`
  - `ACTION_BTN`: `IO9`

## Current Task Status
- Phase 1: Schematic Capture in KiCad.
- Power Management and ESP32 Core net connections finalized and documented.
- Project Git repository initialized and pushed to GitHub.

## Outstanding Issues
- Need to draft KiCad net connections for the remaining blocks (LM358 Analog Front-End, TM1637 Display).
