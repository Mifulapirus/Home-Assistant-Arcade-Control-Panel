# HA Deck v1.0 - Hardware Documentation

Home Assistant Control Deck Version 1.0 with ESP32-WROOM-32

## Hardware Overview

### Microcontroller
- **ESP32-WROOM-32** (NodeMCU Dev Board)
- Dual-core 240 MHz processor
- Built-in WiFi and Bluetooth
- 3.3V logic level

### Components

#### User Interface
- **5× Arcade Style Buttons** - User input controls
- **5× LEDs** - Individual button illumination (one per button)
- **1.3" OLED I2C Display** - Status and information display
- **Buzzer** - Audio feedback

#### Sensors
- **DHT22** - Temperature and humidity sensor
- **RCWL-0516** - Microwave radar motion sensor

## Pin Assignment

### Buttons (Digital Input with Internal Pull-ups)
| Component | GPIO Pin | Notes |
|-----------|----------|-------|
| Button 1  | GPIO17   | Active LOW |
| Button 2  | GPIO19   | Active LOW |
| Button 3  | GPIO33   | Active LOW |
| Button 4  | GPIO26   | Active LOW |
| Button 5  | GPIO27   | Active LOW |

### LEDs (Digital Output)
| Component | GPIO Pin | Current Limiting |
|-----------|----------|------------------|
| LED 1     | GPIO16   | 220Ω resistor |
| LED 2     | GPIO18   | 220Ω resistor |
| LED 3     | GPIO32   | 220Ω resistor |
| LED 4     | GPIO25   | 220Ω resistor |
| LED 5     | GPIO14   | 220Ω resistor |

### I2C Display
| Function | GPIO Pin | Notes |
|----------|----------|-------|
| SDA      | GPIO21   | Default I2C data |
| SCL      | GPIO22   | Default I2C clock |

### Sensors
| Component    | GPIO Pin | Function |
|--------------|----------|----------|
| DHT22        | GPIO04   | Digital data line |
| RCWL-0516    | GPIO23   | Digital output |

### Audio
| Component | GPIO Pin | Notes |
|-----------|----------|-------|
| Buzzer    | GPIO13   | PWM capable |

### Power
- **VCC**: 3.3V for logic-level components
- **VIN**: 5V for sensors requiring higher voltage (DHT22, RCWL-0516)
- **GND**: Common ground

## Circuit Design Notes

### Button Configuration
- Buttons wired between GPIO and GND
- Internal pull-up resistors enabled in firmware (no external resistors needed)
- Debouncing handled in software

### LED Configuration
- Each LED in series with 220Ω current-limiting resistor
- Connected between GPIO and GND
- Forward voltage: ~2V, forward current: ~5-10mA

### I2C Display
- Standard I2C interface
- 3.3V compatible
- Address: Typically 0x3C or 0x3D

### Sensors
- **DHT22**: Single-wire digital interface, requires 3.3-5V
- **RCWL-0516**: 4-28V input, 3.3V compatible output

## Firmware

### Platform
- **ESPHome** - YAML configuration for Home Assistant integration

### Key Features
- Native Home Assistant API
- OTA (Over-The-Air) updates
- Real-time sensor monitoring
- Button press events
- LED control
- Display management

## GPIO Usage Summary

**Total GPIOs Used: 15**
- 5× Button inputs
- 5× LED outputs
- 2× I2C (shared bus)
- 1× DHT22 sensor
- 1× Motion sensor
- 1× Buzzer

**Available for expansion:** GPIO pins still available for future features

## Design Decisions

### Why ESP32-WROOM-32 over ESP8266?
The ESP32 was chosen over the ESP8266 (used in v0.1) due to:
- **GPIO count**: Need 13+ GPIOs for all components
- **Processing power**: Dual-core for better multitasking
- **Native I2C**: Better support for multiple I2C devices
- **Future expansion**: Additional pins available

### Pin Selection Rationale
- Avoided boot-sensitive pins (GPIO0, GPIO2, GPIO5, GPIO12, GPIO15)
- Used default I2C pins (GPIO21/22) for reliability
- Selected general-purpose pins for buttons (GPIO17, 19, 33, 26, 27) to enable internal pull-ups
- Distributed LED pins across available GPIOs (GPIO16, 18, 32, 25, 14)
- PWM-capable pins used for buzzer (GPIO13) and LED control
- Sensor pins carefully selected to avoid conflicts (GPIO04, GPIO23)

## Files in This Directory

- `HA_Deck_v1.kicad_pro` - KiCad project file
- `HA_Deck_v1.kicad_sch` - Schematic design
- `HA_Deck_v1.kicad_pcb` - PCB layout
- `README.md` - This file

**Firmware files are located in:** `../../Software/`

## Development Status

- [x] Component selection
- [x] Pin assignment
- [x] Initial schematic created
- [x] ESPHome firmware configuration
- [ ] Complete schematic wiring
- [ ] PCB layout design
- [ ] Footprint assignment
- [ ] PCB fabrication
- [ ] Assembly and testing

## Version History

**v1.0** (2026-02-07)
- Initial design with ESP32-WROOM-32
- 5 buttons with individual LEDs
- OLED display, DHT22, RCWL-0516 sensors
- Buzzer for audio feedback

---

*For main project information, see the [root README](../../README.md)*
