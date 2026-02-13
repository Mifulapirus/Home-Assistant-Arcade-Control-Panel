# ESPHome Setup Guide for HA Deck v1.0

## Prerequisites

1. **ESPHome installed** (via Home Assistant Add-on or standalone)
2. **USB cable** to connect ESP32 to computer
3. **Home Assistant** instance running

## Installation Methods

### Method A: Home Assistant Add-on (Recommended)

If you're using ESPHome via Home Assistant add-on:

1. **Install ESPHome Add-on**
   - Settings → Add-ons → Add-on Store
   - Search "ESPHome" → Install
   - Start the add-on

2. **Open ESPHome Web UI**
   - Click "Open Web UI" in the add-on
   - Upload `ha_deck_v1.yaml` file
   - The add-on manages the config directory

3. **Configure Secrets**
   - In ESPHome Web UI, click the overflow menu (⋮) → Secrets
   - Enter your WiFi credentials
   - For API key, choose one of these options:
     - **Option A (Easiest)**: Leave blank and ESPHome generates it automatically on first compile
     - **Option B (Manual)**: Generate a key using Python and paste it:
       ```python
       import base64, os
       print(base64.b64encode(os.urandom(32)).decode())
       ```
       Run this in any Python environment and paste the output as the `api_encryption_key`
     - **Option C (Copy-paste)**: Use this example (recommended for testing):
       ```
       api_encryption_key: "TW93QXEzQmhWeUZzTW56RVhxNS9LaVVudHl0MVN3MVJ0bmc="
       ```
       ⚠️ **Note**: For production, generate your own key using Option B

4. **Upload to Device**
   - Connect ESP32 via USB
   - Click the device in ESPHome
   - Select "Install" → "Connect to this computer via USB"
   - Select your USB port

### Method B: Standalone ESPHome (Terminal)

If using standalone ESPHome installation:

1. **Install ESPHome**
   ```bash
   pip3 install esphome
   ```

2. **Generate API Encryption Key**
   
   Choose one of these methods:
   
   **Option A - Easiest (Auto-generate)**
   
   Just leave the key blank in secrets.yaml:
   ```yaml
   api_encryption_key: ""
   ```
   ESPHome will generate it automatically on first compile and display it in the logs.
   
   **Option B - Generate with Python** (recommended for production)
   
   ```bash
   python3 -c "import base64, os; print(base64.b64encode(os.urandom(32)).decode())"
   ```
   
   Copy the output and paste it into `secrets.yaml`
   
   **Option C - Manual Generation (OpenSSL)**
   
   If openssl is available:
   ```bash
   openssl rand -base64 32
   ```
   
   Copy the output into `secrets.yaml`

3. **Configure Secrets**
   
   Edit `secrets.yaml`:
   ```yaml
   wifi_ssid: "YourActualWiFiName"
   wifi_password: "YourActualWiFiPassword"
   api_encryption_key: "base64EncodedKey=="
   ota_password: "ChooseAStrongPassword"
   ap_password: "FallbackPassword"
   ```

4. **First Upload (via USB)**
   
   ```bash
   esphome run ha_deck_v1.yaml
   ```
   Select the USB serial port when prompted.

5. **Subsequent Updates (OTA)**
   
   After first upload, update wirelessly:
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

### Text Sensors
- `text_sensor.hardware_version` - Displays "HA Deck 1.0"

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

### API Key Generation Issues

**"esphome" command not found**

This is normal if ESPHome isn't installed as a CLI tool. You have these options:

1. **Let ESPHome auto-generate** (Recommended for addon users)
   - Leave `api_encryption_key: ""` in secrets.yaml
   - ESPHome generates it on first compile
   - Check the logs to see the generated key

2. **Generate with Python** (Works anywhere)
   ```bash
   python3 -c "import base64, os; print(base64.b64encode(os.urandom(32)).decode())"
   ```
   Copy the output and paste into `secrets.yaml`

3. **Generate with PHP** (If you have PHP installed)
   ```bash
   php -r "echo base64_encode(random_bytes(32));"
   ```

4. **Use an online tool** (For testing only, not production)
   - https://generate.plus/en/base64
   - Generate 32 random bytes and base64 encode

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

The current config automatically gets timezone from Home Assistant:

```yaml
time:
  - platform: homeassistant
    id: ha_time
    timezone: "America/New_York"  # Gets your HA timezone
```

To override with a specific timezone:
```yaml
time:
  - platform: homeassistant
    id: ha_time
    timezone: "Europe/London"  # Your timezone
```

### Use NTP Instead of Home Assistant

If you prefer to get time from NTP (independent of Home Assistant):

```yaml
time:
  - platform: sntp
    id: ha_time
    timezone: "America/New_York"
    servers:
      - 0.pool.ntp.org
      - 1.pool.ntp.org
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
