# GrowSensor – Dual-Build (Classic vs. Pro)

Diese Firmware unterstützt jetzt zwei Build-Varianten:

- **GrowSensor – v0.3.3 (Classic ESP32)** – Legacy-Build für klassische DevKit-Boards (Standard ESP32, 4MB Flash).
- **GrowSensor – v0.4.0 (ESP32 Pro 16MB)** – Neue, optimierte Firmware für ESP32-Module mit externer Antenne, 16MB Flash und PSRAM. Aktueller Fokus: Performance, Stabilität, saubere Task-Trennung (UI / Telemetrie / Cloud) und langfristige Cloud-Datenhaltung.

## PlatformIO Builds

Standard-Ziel ist die Pro-Variante:

- Pro (empfohlen): `pio run -e esp32pro16m`
- Classic: `pio run -e esp32classic`

### Einstellungen Pro-Board
- Board: `esp-wrover-kit` (16MB + PSRAM)
- Partitionstabelle: `partitions/growsensor_16mb.csv`
- Build-Flags: LTO + O2, mDNS aktiv
- Firmware-String: `GrowSensor – v0.4.0 (ESP32 Pro 16MB)`

### Classic-Board
- Board: `esp32dev`
- Build-Flags: O0/Os ohne LTO, mDNS deaktiv
- Firmware-String: `GrowSensor – v0.3.3 (Classic ESP32)`

## Hinweise für Migration
- WebUI, Sensor-Tiles, VPD, Lux→PPFD, Cloud-Logging (Nextcloud/WebDAV), Summaries und Reports bleiben funktionsgleich.
- Cloud-Payloads enthalten jetzt `firmware`, `target` und `channel`, damit Classic/Pro klar getrennt sind.
- Der Pro-Build nutzt PSRAM (falls vorhanden) für Assets/Buffer; Classic bleibt unverändert.

## Flash / Speicher
- Pro: Dual-OTA + großer SPIFFS-Bereich für gecachte Assets/Logs.
- Classic: Standard-Partitionierung unverändert.

## LED & Silent Mode (Vorbereitung)
- Pro: Board-LEDs standardmäßig aus (per Code-Flag vorbereitet; UI-Schalter kann nachgerüstet werden).

## Raspberry-Pi-Bridge (Vorbereitet)
- Die bestehende Pi-Bridge-Logik bleibt erhalten; Architekturänderungen blockieren künftige Erweiterungen nicht.

## Changelog (Auszug)
- Neue PlatformIO-Umgebung **esp32pro16m** als Default.
- Klassische Umgebung **esp32classic** bleibt erhalten.
- Firmware-Strings und Status-/Cloud-Payloads tragen jetzt Board/Channel-Infos.
- Partitionstabelle für 16MB hinzugefügt (`partitions/growsensor_16mb.csv`).
