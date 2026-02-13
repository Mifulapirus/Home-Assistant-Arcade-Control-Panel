# ESPHome Setup Guide for HA Deck v1.0

## Prerequisites

1. **ESPHome installed** (via Home Assistant Add-on or standalone)
2. **USB cable** to connect ESP32 to computer
3. **Home Assistant** instance running

## Initial Setup

### 1. Generate Encryption Key

Run this command to generate your API encryption key:
```bash
esphome -s api_key $(openssl rand -base64 32) compile ha_deck_v1.yaml
```

Or let ESPHome generate it automatically on first compile.

### 2. Configure Secrets

Edit `secrets.yaml` with your actual values:

```yaml
wifi_ssid: "YourActualWiFiName"
wifi_password: "YourActualWiFiPassword"
api_encryption_key: "base64EncodedKey=="
ota_password: "ChooseAStrongPassword"
ap_password: "FallbackPassword"
```

### 3. First Upload (via USB)

```bash
esphome run ha_deck_v1.yaml
```

Select the USB serial port when prompted.

### 4. Subsequent Updates (OTA)

After the first upload, you can update wirelessly:

```bash
esphome run ha_deck_v1.yaml
```

Select the network device when prompted.

## Home Assistant Integration

### 1. Auto-Discovery

The device should appear in Home Assistant under **Settings → Devices & Services → ESPHome**

### 2. Manual Integration

If auto-discovery doesn't work:
1. Go to **Settings → Devices & Services**
2. Click **Add Integration**
3. Search for **ESPHome**
4. Enter the device IP address
5. Enter the encryption key from `secrets.yaml`

## Available Entities

### Buttons (Binary Sensors)
- `binary_sensor.blue_button`
- `binary_sensor.green_button`
- `binary_sensor.red_button`
- `binary_sensor.white_button`
- `binary_sensor.yellow_button`
- `binary_sensor.arcade_panel_motion`

### LEDs (Lights)
- `light.blue_led`
- `light.green_led`
- `light.red_led`
- `light.white_led`
- `light.yellow_led`

### Sensors
- `sensor.arcade_panel_temperature`
- `sensor.arcade_panel_humidity`
- `sensor.arcade_panel_heat_index`
- `sensor.arcade_panel_dew_point`
- `sensor.arcade_panel_wifi_signal`

### Services

#### Display Custom Text
```yaml
service: esphome.ha_arcade_panel_display_text
data:
  text: "Hello World!"
  duration: 5000  # milliseconds
  font_size: 2     # 1=small, 2=medium, 3=large
```

#### Play Buzzer Sequence
```yaml
service: esphome.ha_arcade_panel_play_buzzer_sequence
data:
  sequence: 1
```

**Available Sequences:**
1. Short beep (100ms)
2. Beep-beep (double tap)
3. Long beep (500ms)
4. Triple beep
5. Rising tone
6. Alert pattern
7. Success chirp
8. Error buzz
9. Doorbell
10. SOS pattern

## Example Automations

### Display Message on Button Press

```yaml
automation:
  - alias: "Blue Button Display Message"
    trigger:
      - platform: state
        entity_id: binary_sensor.blue_button
        to: 'on'
    action:
      - service: esphome.ha_arcade_panel_display_text
        data:
          text: "Blue Activated!"
          duration: 3000
          font_size: 2
      - service: esphome.ha_arcade_panel_play_buzzer_sequence
        data:
          sequence: 7
```

### Temperature Alert

```yaml
automation:
  - alias: "High Temperature Alert"
    trigger:
      - platform: numeric_state
        entity_id: sensor.arcade_panel_temperature
        above: 30
    action:
      - service: esphome.ha_arcade_panel_display_text
        data:
          text: "High Temp!"
          duration: 10000
          font_size: 3
      - service: esphome.ha_arcade_panel_play_buzzer_sequence
        data:
          sequence: 6
      - service: light.turn_on
        entity_id: light.red_led
```

### Motion Detection with Visual Feedback

```yaml
automation:
  - alias: "Motion Detected Feedback"
    trigger:
      - platform: state
        entity_id: binary_sensor.arcade_panel_motion
        to: 'on'
    action:
      - service: light.turn_on
        entity_id: light.white_led
      - service: esphome.ha_arcade_panel_play_buzzer_sequence
        data:
          sequence: 1
      - delay: '00:00:02'
      - service: light.turn_off
        entity_id: light.white_led
```

### LED Pattern on Startup

```yaml
automation:
  - alias: "Arcade Panel Startup Sequence"
    trigger:
      - platform: homeassistant
        event: start
    action:
      - service: light.turn_on
        entity_id: light.blue_led
      - delay: '00:00:00.2'
      - service: light.turn_on
        entity_id: light.green_led
      - delay: '00:00:00.2'
      - service: light.turn_on
        entity_id: light.red_led
      - delay: '00:00:00.2'
      - service: light.turn_on
        entity_id: light.white_led
      - delay: '00:00:00.2'
      - service: light.turn_on
        entity_id: light.yellow_led
      - delay: '00:00:01'
      - service: light.turn_off
        entity_id: 
          - light.blue_led
          - light.green_led
          - light.red_led
          - light.white_led
          - light.yellow_led
```

## Troubleshooting

### Display Not Working
- Check I2C address (0x3C or 0x3D)
- Try changing `model: "SH1106 128x64"` to `SSD1306_128X64`
- Enable I2C scan in logs to verify address

### Buttons Not Responding
- Verify internal pull-ups are enabled
- Check wiring: buttons between GPIO and GND
- Increase debounce in binary_sensor config if needed

### WiFi Connection Issues
- Check SSID and password in secrets.yaml
- Verify WiFi coverage in device location
- Use fallback AP mode to reconfigure

### OTA Updates Failing
- Ensure device is on same network
- Check firewall settings
- Try USB upload if OTA consistently fails

## Configuration Customization

### Change Timezone
Edit the `time:` component:
```yaml
time:
  - platform: homeassistant
    id: ha_time
    timezone: "Europe/London"  # Your timezone
```

### Adjust Display Update Rate
Change `update_interval` in display component:
```yaml
display:
  - platform: ssd1306_i2c
    update_interval: 500ms  # Faster updates
```

### Change Page Cycle Time
Modify the interval:
```yaml
interval:
  - interval: 10s  # Show each page for 10 seconds
```

### Adjust DHT22 Update Frequency
```yaml
sensor:
  - platform: dht
    update_interval: 60s  # Update every minute
```

## Hardware Notes

- All LEDs use 220Ω current-limiting resistors
- Buttons use internal ESP32 pull-up resistors (no external resistors needed)
- DHT22 sensor may need a 4.7kΩ pull-up resistor on data line (depends on module)
- OLED display is 3.3V compatible

## Support

For issues or questions:
- Check ESPHome documentation: https://esphome.io
- Review logs: `esphome logs ha_deck_v1.yaml`
- See project README in parent directory
