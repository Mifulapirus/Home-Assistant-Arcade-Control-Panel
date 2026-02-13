# Software - HA Arcade Panel Firmware

ESPHome firmware configuration for the Home Assistant Arcade Control Panel.

## Files

- **[ha_deck_v1.yaml](ha_deck_v1.yaml)** - Main ESPHome configuration file
- **[secrets.yaml](secrets.yaml)** - Template for WiFi credentials and API keys
- **[ESPHOME_SETUP.md](ESPHOME_SETUP.md)** - Complete setup and installation guide

## Hardware Version

This firmware is designed for **HA Deck 1.0** hardware.

## Quick Start

1. Copy `secrets.yaml` to your ESPHome config directory
2. Edit `secrets.yaml` with your WiFi credentials
3. Upload `ha_deck_v1.yaml` to your ESP32 via USB:
   ```bash
   esphome run ha_deck_v1.yaml
   ```
4. After first upload, updates can be done wirelessly (OTA)

## Features

### Device Information
- **Hardware Version Entity**: Displays "HA Deck 1.0" in Home Assistant

### User Interface
- **5 Color-Coded Buttons**: Blue, Green, Red, White, Yellow
- **5 Individual LEDs**: One for each button with independent control
- **OLED Display**: 1.3" I2C display showing time, temperature, and custom messages
- **Buzzer**: 10 pre-programmed sound patterns

### Sensors
- **DHT22**: Temperature and humidity monitoring
- **RCWL-0516**: Motion detection
- **Calculated Sensors**: Heat index, dew point
- **WiFi Signal**: Network strength monitoring

### Services

#### Display Custom Text
```yaml
service: esphome.ha_arcade_panel_display_text
data:
  text: "Hello!"
  duration: 5000
  font_size: 2
```

#### Play Buzzer Sequence
```yaml
service: esphome.ha_arcade_panel_play_buzzer_sequence
data:
  sequence: 1  # 1-10
```

## Pin Mapping (HA Deck 1.0)

| Component | GPIO | Function |
|-----------|------|----------|
| Blue Button | GPIO17 | Input with pull-up |
| Green Button | GPIO19 | Input with pull-up |
| Red Button | GPIO33 | Input with pull-up |
| White Button | GPIO26 | Input with pull-up |
| Yellow Button | GPIO27 | Input with pull-up |
| Blue LED | GPIO16 | Output |
| Green LED | GPIO18 | Output |
| Red LED | GPIO32 | Output |
| White LED | GPIO25 | Output |
| Yellow LED | GPIO14 | Output |
| OLED SDA | GPIO21 | I2C Data |
| OLED SCL | GPIO22 | I2C Clock |
| DHT22 | GPIO04 | 1-Wire Data |
| RCWL-0516 | GPIO23 | Digital Input |
| Buzzer | GPIO13 | PWM Output |

## Documentation

See [ESPHOME_SETUP.md](ESPHOME_SETUP.md) for:
- Detailed installation instructions
- Home Assistant integration guide
- Example automations
- Troubleshooting tips
- Configuration customization

## Related Hardware

Hardware design files are located in: `../Hardware/HA Deck 1.0/`

---

*Part of the Home Assistant Control Panel project*
