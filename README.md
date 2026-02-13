# Home Assistant Control Panel

Home Assistant Control Panel project, including hardware designs and firmware for a smart home interface.

## Project Structure

- **Hardware/** - KiCad PCB designs and schematics
  - `HA Deck 0.1/` - Initial ESP8266-based prototype
  - `HA Deck 1.0/` - Current ESP32-based design
- **Software/** - ESPHome firmware configuration
  - Complete firmware for HA Deck 1.0
  - Setup guides and example automations

## Hardware Versions

### Version 0.1
- Based on **Wemos D1 Mini** (ESP8266)
- Initial prototype design
- KiCad project files located in `Hardware/HA Deck 0.1`

### Version 1.0 (Current Development)
- Based on **ESP32-WROOM-32** module
- Hardware upgrade driven by I/O requirements:
  - 5 arcade buttons with individual LEDs
  - 1.3" OLED I2C display
  - DHT22 temperature/humidity sensor
  - RCWL-0516 motion sensor
  - Buzzer for audio feedback
- KiCad project files located in `Hardware/HA Deck 1.0`
- Firmware located in `Software/`
![HA Deck v1.0 Schematic](Hardware/HA%20Deck%201.0/1.0%20schematic.jpg)

## Firmware

- **ESPHome** - Used for firmware development and Home Assistant integration
- Enables easy configuration and OTA updates
- Native Home Assistant API support
- Color-coded buttons (Blue, Green, Red, White, Yellow)
- Custom display messages
- 10 buzzer sound patterns
- Full sensor integration

## Technologies Used

- KiCad for PCB design
- ESP32-WROOM-32 module (Version 1.0)
- Wemos D1 Mini / ESP8266 (Version 0.1)
- ESPHome firmware framework
- Home Assistant integration

## Getting Started

### Hardware
1. Open KiCad project files in `Hardware/HA Deck 1.0/`
2. Review schematic and PCB layout
3. Export Gerber files for fabrication

### Firmware
1. Navigate to `Software/` directory
2. Follow instructions in `ESPHOME_SETUP.md`
3. Configure `secrets.yaml` with your WiFi credentials
4. Upload firmware to ESP32

## Deployment

1. Fabricate the PCB using exported Gerber files
2. Assemble components according to schematic
3. Flash ESPHome firmware via USB
4. Device auto-discovers in Home Assistant
5. Configure automations and integrations

