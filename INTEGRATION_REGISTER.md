# Integration Register

Stand: 2026-09-02

| ID | Integration / Pfad | Geräte | Read | Write | Update/Latenz | Abhängigkeit | Status / Notes |
|---|---|---|---|---|---|---|---|
| INT-HA-SHELLY | Home Assistant Shelly | Shelly Pro 3EM, Shelly Plug S Gen3 | YES | geräteabhängig | VERIFY | lokal/HA | ACTIVE |
| INT-ZENDURE | Zendure ↔ Home Assistant | SolarFlow 2400 AC, SolarFlow 800 Plus | YES | YES / im Projekt genutzt | VERIFY | konkreter Integrationspfad importieren | ACTIVE / IMPORT DETAILS |
| INT-HAME-MQTT | HAME Relay + hm2mqtt | Marstek Venus D | YES | VERIFY | ca. 1 min Telemetrie | Relay/MQTT/Add-on | ACTIVE / PARTIAL |
| INT-MARSTEK-RS485 | Modbus RS485 | Marstek Venus D | UNKNOWN | UNKNOWN | UNKNOWN | Hardware/Protokoll | DISCUSSED / NOT VERIFIED |
| INT-SWITCHBOT | Home Assistant / SwitchBot | Thermo-Hygrometer | YES | n/a | VERIFY | Integration | ACTIVE/KNOWN |
| INT-GOVEE | Home Assistant / Govee | Klima-Sensoren | YES | n/a | VERIFY | Integration | ACTIVE/KNOWN |
| INT-ACINFINITY | AC Infinity ↔ HA/GitHub | AC Infinity Geräte | UNKNOWN | UNKNOWN | UNKNOWN | noch zu wählen | OPEN |

## Control-Ownership-Regel

Für jede beschreibbare Stellgröße muss dokumentiert werden, welcher Regler aktuell autoritativ ist:

- Home Assistant
- Hersteller-HEMS
- Hersteller-App/Zeitplan
- interne Geräteautomatik
- anderer externer Controller

HEMS und Home Assistant dürfen nicht parallel dieselben Limits regeln, sofern die Koordination nicht ausdrücklich verifiziert ist.

## Integrations-Pflichtfelder

Bei vollständiger Inventur ergänzen:

- konkrete HA-Integration/Add-on/Custom Component
- Version
- lokale oder Cloud-Verbindung
- Authentisierungspfad ohne Secret-Wert
- Read-Fähigkeiten
- Write-Fähigkeiten
- bekannte Stellgrößen
- nominale und reale Updatefrequenz
- Offline-/Reconnect-Verhalten
- Restore-/Cache-Verhalten
- bekannte Firmware-/API-Abhängigkeiten
- abhängige Automationen

## HAME Relay / hm2mqtt

Bekannter kritischer Punkt:

- ungefähr 1 Update pro Minute kann für Monitoring genügen, ist aber für schnelle Netzregelung potenziell zu langsam.
- Ein fehlender SOC darf nicht durch den letzten bekannten SOC ersetzt und als live behandelt werden.
- Read- und Write-Fähigkeit separat bewerten; aus erfolgreichem Lesen nicht automatisch Steuerbarkeit ableiten.