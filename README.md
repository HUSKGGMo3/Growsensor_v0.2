diff --git a/README.md b/README.md
index 6880b0729fadc6d4245fadb94ab873eb92e2c184..47805f44897d1f6b8a928f9b8c9044013cbb0f5e 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,85 @@
-# Growcontroler_v0.1
-Growcontrollerbymaurice
+# Growcontroller v0.1
+
+All-in-one GrowSensor firmware for ESP32 (Sensorgehäuse v0.3, Software v0.1). The firmware opens an on-device Wi-Fi access point for first boot, hosts a captive web UI with live readings, and stores Wi-Fi plus spectrum preferences for PPFD conversion.
+
+## Features
+- **Captive portal onboarding**: boots as access point `GrowSensor-Setup` (password `growcontrol`) when no Wi-Fi is configured; redirects to the web UI to save credentials.
+- **Live dashboard**: BH1750 lux → PPFD with selectable LED spectrum (full, white, bloom), CO₂ (MH-Z14), ambient temp/humidity (SHT31), and leaf temp (MLX90614).
+- **Lightweight charts**: inline canvas chart showing recent lux/CO₂/temperature without external CDN dependencies.
+- **Persistent settings**: Wi-Fi credentials and spectrum preference stored in NVS `Preferences`.
+- **Home Assistant friendly**: JSON endpoint for REST sensors; captive portal can be disabled by saving Wi-Fi credentials.
+
+## Hardware wiring
+| Sensor | Notes | ESP32 pins |
+| --- | --- | --- |
+| BH1750 | I²C address `0x23` | SDA `21`, SCL `22` |
+| SHT31 | I²C address `0x44` | SDA `21`, SCL `22` |
+| MLX90614-DCI | I²C address `0x5A` (default) | SDA `21`, SCL `22` |
+| MH-Z14 CO₂ | UART 9600 bps | RX `16` (ESP32), TX `17` (ESP32) |
+
+> Adjust `I2C_SDA_PIN`, `I2C_SCL_PIN`, `CO2_RX_PIN`, and `CO2_TX_PIN` in `src/main.cpp` if your board uses different pins.
+
+## Build & flash (PlatformIO)
+1. Install [PlatformIO CLI](https://docs.platformio.org/) or the VS Code extension.
+2. Connect the ESP32 via USB.
+3. From the repo root run:
+   ```sh
+   pio run -t upload
+   ```
+4. Open the serial monitor (115200 baud) to see boot logs:
+   ```sh
+   pio device monitor -b 115200
+   ```
+
+## First boot / captive portal
+1. Power on the device. It starts `GrowSensor-Setup` (password `growcontrol`).
+2. Connect with your phone/laptop; the OS will show "Bitte im Netzwerk anmelden". If not, open `http://192.168.4.1`.
+3. Enter your Wi-Fi SSID and password, click **Verbinden & Speichern**. The device restarts and joins your network.
+4. Browse to the shown IP (serial log) or check your router to reach the dashboard.
+
+### Changing LED spectrum
+Use the **Spektrum wählen** box in the dashboard to switch conversion factors:
+- Vollspectrum (Lux × 0.014)
+- White (Lux × 0.0157)
+- Blütekanal (Lux × 0.0175)
+
+### Reset Wi-Fi / factory data
+Click **WLAN Reset** or call `POST /api/reset`. The device clears saved credentials and reboots into AP mode.
+
+## API
+- `GET /api/telemetry` → `{ lux, ppfd, co2, temp, humidity, leaf }`
+- `GET /api/settings` → `{ channel, wifi, ssid }`
+- `POST /api/settings` (`channel=full_spectrum|white|bloom`)
+- `POST /api/wifi` (`ssid=...&pass=...`) saves credentials and restarts
+- `POST /api/reset` clears preferences and restarts
+
+## Home Assistant example (REST sensors)
+```yaml
+rest:
+  - resource: http://<device-ip>/api/telemetry
+    scan_interval: 30
+    sensor:
+      - name: GrowSensor Lux
+        value_template: "{{ value_json.lux }}"
+        unit_of_measurement: lx
+      - name: GrowSensor PPFD
+        value_template: "{{ value_json.ppfd }}"
+        unit_of_measurement: "µmol/m²/s"
+      - name: GrowSensor CO2
+        value_template: "{{ value_json.co2 }}"
+        unit_of_measurement: ppm
+      - name: GrowSensor Temp
+        value_template: "{{ value_json.temp }}"
+        unit_of_measurement: °C
+      - name: GrowSensor Humidity
+        value_template: "{{ value_json.humidity }}"
+        unit_of_measurement: "%"
+      - name: GrowSensor Leaf Temp
+        value_template: "{{ value_json.leaf }}"
+        unit_of_measurement: °C
+```
+
+## Notes
+- The included lux→PPFD factors are practical approximations; refine them with your own calibration if you have a quantum sensor.
+- MH-Z14 auto-calibration is disabled by default (`autoCalibration(false)`). Enable it if your environment allows periodic 400 ppm exposure.
+- This firmware was authored in a CI-less environment; please build/flash on hardware to validate wiring and sensor addresses.
