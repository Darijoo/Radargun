# Pocket Radar Gun Project

## Project Architecture
1. **Phase 1: Offline Hardware Device** (PCB, Sensor, Display)
2. **Phase 2: Cloud Integration** (Wi-Fi data logging)
3. **Phase 3: Web Dashboard** (Player analytics)

## Component Bill of Materials (BOM)
- **Microcontroller**: ESP32-S3-WROOM-1 (Bare module, surface mount)
- **Sensor**: HB100 Microwave Doppler Sensor (10.525 GHz)
- **Analog Front-End**: LM358 Op-Amp (2-stage active band-pass filter, ~10000x gain, 2.5V virtual ground)
- **Power Management**:
  - 3.7V Li-Po Battery
  - TP4056 + DW01A (USB-C charging and protection)
  - MT3608 Boost Converter (Stepping up to 5V for HB100)
  - AP2112K-3.3 or ME6211 Ultra-Low Dropout Regulator (3.3V for ESP32)
  - AO3401 P-Channel MOSFET (High-side switching to power down HB100)
  - Voltage divider (2x 100kΩ) for battery level sensing
- **User Interface**: TM1637 4-digit 7-segment LED display, tactile action button

## Pinout Mappings
*(To be populated as schematic capture progresses)*

## Current Task Status
- Phase 1: Schematic Capture in KiCad.
- Power Management block (Block 1) conceptualization complete.
- Project Git repository initialized.

## Outstanding Issues
- Need to finalize specific KiCad net connections for the Power Management block.
