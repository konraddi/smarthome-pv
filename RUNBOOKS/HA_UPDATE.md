# Runbook – Home Assistant Update

## Vor Update

- aktuelle HA-Version dokumentieren
- Zielversion und relevante Release Notes/Breaking Changes prüfen
- betroffene Integrationen identifizieren
- Backupstatus klären
- bei produktiver Änderung Freigabe-/Rollbackpfad beachten

## Schwerpunkt dieses Projekts

Besonders prüfen:

- Shelly
- Templates
- MQTT
- Custom Components/Add-ons für Zendure/Marstek/HAME
- Energy Dashboard / Statistik
- Automationssyntax und Actions
- Recorder/Historie

## Nach Update

1. HA startet fehlerfrei.
2. kritische Entities verfügbar.
3. keine unerwarteten Restore-/unknown-Phasen bleiben bestehen.
4. Automationen geladen.
5. Integrationen verbunden.
6. Speicherzustände plausibel.
7. relevante Acceptance Tests durchführen.
8. Logs auf neue Fehler prüfen.
9. Version und Ergebnis dokumentieren.

## Wichtig

Ein erfolgreicher HA-Start beweist nicht, dass Speicherautomationen korrekt arbeiten. Für netzrelevante Steuerungen physische Wirkung prüfen.