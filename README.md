# Growsensor – ESP32 Monitoring Node

> **Status:** v0.2.0 community preview (untested)  
> **Scope:** Telemetry and visualization only – no actuator control.

Growsensor is a lightweight ESP32 firmware that reads common grow-room sensors, computes PPFD/VPD, and serves a responsive WebUI for live dashboards, charts, Wi‑Fi setup, and partner/supporter info. It is intended as a monitoring companion; a separate future **Growcontroller** project will handle automation.

## Highlights
- **Sensor coverage:** Lux → PPFD, Temperature, Humidity, CO₂, Leaf temperature.
- **Growth insights:** VPD calculation with stage presets (seedling, veg, bloom, late bloom) and target status.
- **Web experience:** Captive-portal setup, live dashboard, 24h history, downloadable logs, and partner list.
- **Dev unlock:** Advanced changes (Wi‑Fi, sensor toggles, partner edits) require entering the code `Test1234#` in the UI; day-to-day viewing stays open.
- **Config persistence:** Preferences stored via ESP32 `Preferences` with distinct keys for Wi‑Fi and admin credentials to avoid collisions.

## Supported hardware
- **MCU:** ESP32 (Arduino framework).
- **Sensors:** BH1750 (Lux), SHT31/SHT30 (Temp/Humidity), MLX90614 (Leaf Temp), MH-Z19 series (CO₂).
- **Pins:** I²C and CO₂ UART pins are defined near the top of `src/main.cpp` (`I2C_SDA_PIN`, `I2C_SCL_PIN`, `CO2_RX_PIN`, `CO2_TX_PIN`).

## Build & flash (PlatformIO)
1. Install PlatformIO CLI or VS Code + PlatformIO extension.
2. Connect the ESP32 via USB.
3. Build & flash:
   ```sh
   pio run -t upload
   ```
4. Monitor (115200 baud):
   ```sh
   pio device monitor -b 115200
   ```

## Using the WebUI
- After boot, connect to the device Wi‑Fi (captive portal) or to its LAN IP.
- For regular viewing (dashboards, logs, partner list) no code is required.
- To change Wi‑Fi settings, toggle sensors, or edit partners, click **Dev-Modus** in the header and enter `Test1234#` to unlock advanced controls.

## Notes & roadmap
- This firmware is for **monitoring only**; relays/automation are not driven yet.
- Future work: Growcontroller automation, broader sensor coverage, stability hardening.

## License & contributing
- Non-commercial open source license (see `LICENSE`). Commercial use requires explicit permission.
- Contributions via pull requests are welcome (see `CONTRIBUTING.md`). Please read `DISCLAIMER.md` before deploying.
- Deutsche Kurzfassung: siehe `README.de.md`.
