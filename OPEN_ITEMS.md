# Open Items

Stand: 2026-09-02

Nur offene Punkte. Erledigte Punkte werden entfernt und bei Relevanz in `PROJECT_TIMELINE.md` dokumentiert.

## OI-001 – Repository-Privacy entscheiden

Priorität: **HOCH**

Das Repo ist derzeit öffentlich. Vor Ablage detaillierter Entity-Inventare mit sensibler Topologie, externen Zugangsdaten oder Klima-/Anwesenheitszeitreihen entscheiden, ob das Repo privat werden soll.

## OI-002 – Aktuelle Home-Assistant-Version und Systembasis inventarisieren

Priorität: MITTEL

Benötigt:

- HA Core-Version
- Installationsart
- Recorder-/DB-Typ
- relevante Add-ons/Custom Components
- Backup-/Restore-Stand

## OI-003 – Zentrale Netzleistungs-Entity verifizieren

Priorität: **HOCH**

Benötigt:

- exakte Entity-ID des Shelly-Pro-3EM-Netto-Sensors
- Vorzeichenkonvention
- Einheit
- Updateintervall
- Freshness
- Template-Herkunft

Ohne das keine endgültige kanonische Energieflussformel.

## OI-004 – Vollständige Entity-Inventur importieren

Priorität: HOCH

Aus aktuellem HA-Stand relevante Entities für PV, Netz, Speicher, Smart Plugs und Klima übernehmen. Keine Rekonstruktion aus alten Chats.

## OI-005 – Aktuelle SolarFlow-2400-AC-Automation importieren

Priorität: **HOCH**

Vollständige aktuelle YAML, Alias/ID, Inputs, Aktoren, Freshness und Failsafe dokumentieren.

## OI-006 – Aktuelle SolarFlow-800-Plus-Automation importieren

Priorität: **HOCH**

Vollständige aktuelle YAML inkl. Übergangsfenstern und regelmäßiger Nachtprüfung dokumentieren.

## OI-007 – Venus-D-Failsafe verifizieren

Priorität: **HOCH**

Prüfen, wie die geänderte 2400-AC-Automation bei Venus-D-SOC `unknown`/`unavailable`/stale tatsächlich reagiert. Acceptance Tests AT-03, AT-08, AT-09 und AT-11 anwenden.

## OI-008 – HAME Relay / hm2mqtt Steuerfähigkeit klären

Priorität: MITTEL

Getrennt erfassen:

- Read-Funktionen
- Write-Funktionen
- reale Updatefrequenz
- Schreib-Latenz
- Topics/Services
- Offline-Verhalten

## OI-009 – Modbus-RS485 für Venus D bewerten

Priorität: MITTEL

Nur weiterverfolgen, wenn direkter stabiler Steuerzugriff benötigt wird. Offizielle/projektspezifisch verifizierte Register/Funktionen erforderlich; nichts raten.

## OI-010 – PV-Modul-/WR-Zuordnung inventarisieren

Priorität: NIEDRIG/MITTEL

29 Module, ungefähr 8–8,5 kWp sind bekannt. Exakte Module, Ausrichtung, Strings und Wechselrichterzuordnung fehlen noch.

## OI-011 – Klima-Datenpipeline umsetzen

Priorität: MITTEL

Abhängigkeiten:

1. Privacy-Entscheidung
2. HA-Recorder-/DB-Pfad
3. Sensor-Whitelist
4. Exportmechanismus
5. Retention

## OI-012 – AC Infinity Integration prüfen

Priorität: MITTEL

Aktuelle Integrationsmöglichkeiten für die tatsächlich vorhandenen Geräte recherchieren und auf lokale/Cloud-Abhängigkeit, Datenrate und Write-Fähigkeit bewerten.

## OI-013 – Energy-Dashboard-Inventur

Priorität: MITTEL

Alle derzeit eingetragenen Netz-, PV-, Batterie- und Verbrauchszähler gegen reale Richtung, `device_class`, `state_class` und Doppelzählung prüfen.