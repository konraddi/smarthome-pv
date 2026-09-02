# Automations

Dieser Ordner ist für die **kanonischen, aktuell aus Home Assistant übernommenen** Automationsquellen gedacht.

## Regeln

- Keine Automation aus Chat-Memory rekonstruieren.
- Vor Erstimport die tatsächlich aktuelle HA-YAML lesen/exportieren.
- Secrets entfernen bzw. durch Secret-Referenzen ersetzen.
- Pro logisch eigenständiger Automation bevorzugt eine Datei.
- Dateiname stabil und beschreibend, z. B. `solarflow_2400ac_energy.yaml`.
- Änderungen immer auch in `AUTOMATION_REGISTER.md` und bei tatsächlichem Betriebsereignis in `PROJECT_TIMELINE.md` reflektieren.

## Header je Automation

Soweit praktisch als Kommentar dokumentieren:

- Automation-ID/Alias
- Zweck
- Control Owner
- kritische Inputs
- Failsafe-Prinzip
- letzte verifizierte HA-Version/Integrationsbasis
- letzter Acceptance-Test-Stand

## Aktuell

Die produktiven SolarFlow-2400-AC- und SolarFlow-800-Plus-YAMLs sind noch **nicht importiert**. Bis dahin sind die Beschreibungen in `AUTOMATION_REGISTER.md` nur ein belegter Funktionsindex, nicht die kanonische YAML.