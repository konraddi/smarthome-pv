# Operating Rhythm

Stand: 2026-09-02

Keine ritualisierte Vollprüfung. Kontrollen werden durch Änderungen, Auffälligkeiten und relevante Zeitpunkte ausgelöst.

## Event-getriebene Reviews

### Nach Home-Assistant-Update

- Breaking Changes der relevanten Integrationen prüfen
- Automationen/Template-Sensoren laden
- kritische Entities verfügbar?
- Restore-/Startup-Verhalten prüfen
- relevante Acceptance Tests ausführen

### Nach Firmware-/App-/Integration-Update eines Speichers

- verfügbare Stellgrößen und Zustände vergleichen
- Entity-/Topic-/API-Änderungen prüfen
- Updateintervall/Latenz prüfen
- kritische Automationen regressionsprüfen
- neue Limits nicht aus Release Notes allein als real übernommen behandeln

### Nach Änderung einer Energieautomation

- Vorzeichen/Freshness prüfen
- YAML/Template validieren
- positive/negative Sollfälle testen
- `unknown`/`unavailable`-Pfad testen
- Command → Device → Physical Effect prüfen
- Timeline/Automation Register aktualisieren

### Nach Änderung einer Entity oder Gerätezuordnung

- Entity Register aktualisieren
- abhängige Automationen suchen
- Energy-Dashboard-Semantik prüfen
- alte Entity nicht still weiterreferenzieren

### Nach neuem Gerät

- Device-ID vergeben
- Integration inventarisieren
- wichtige Entities erfassen
- Control Ownership festlegen
- Energiefluss/Messpunkt dokumentieren
- Acceptance Tests definieren

## Periodische Themen ohne fest eingebrannte Pflichtintervalle

Bei Bedarf bzw. wenn das Projekt wächst regelmäßig prüfen:

- offene Failsafe-Gaps
- stale/unavailable-Statistik kritischer Sensoren
- konkurrierende Automationen/Control Owner
- verwaiste Entities
- Energy-Dashboard-Doppelzählungen
- Backups/Restore-Fähigkeit von HA
- Repository-Secrets/Privacy
- Datenpipeline-Größe und Retention
- Firmware-/HA-Freshness

Konkrete Intervalle erst festlegen, wenn sie betrieblich sinnvoll und vom Nutzer gewünscht sind.

## Incident-Trigger

Bei unerwarteter Netzeinspeisung/-bezug, falscher Speicherreaktion, Sprüngen im Energy Dashboard oder fehlenden Gerätedaten:

1. keine voreilige Parameteroptimierung
2. relevanten Zeitbereich sichern
3. Sensor-Freshness und Vorzeichen prüfen
4. Automation Trace/Log prüfen
5. konkurrierende Regler prüfen
6. physischen Geräte-State prüfen
7. Root Cause erst danach bewerten
8. Fix und Prevention dokumentieren