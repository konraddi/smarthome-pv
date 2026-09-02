# Open Items

Stand: 2026-09-02 – nach Deep Inventory

Nur offene Punkte. Bereits durch den Config-/Automation-Snapshot geklärte Lücken wurden entfernt oder präzisiert.

## OI-001 – Repository-Privacy entscheiden

Priorität: **HOCH**

Das Repo ist derzeit öffentlich.

Vor automatischer Ablage detaillierter Klima-/Verbrauchszeitreihen entscheiden:

- öffentlich lassen oder privat stellen?
- welche Sensoren dürfen dauerhaft gespeichert werden?
- welche Auflösung/Retention ist vertretbar?

Entity-IDs werden aus technischem Grund dokumentiert; Secrets, private IPs und externe Zugangsdaten bleiben ausgeschlossen.

## OI-002 – Home-Assistant-Systembasis live inventarisieren

Priorität: **MITTEL**

Durch den letzten Config-Snapshot bereits bekannt:

- Recorder = SQLite.
- `/config/home-assistant_v2.db`.
- 30 Tage Retention.

Noch live zu ermitteln:

- HA Core-Version.
- Installationsart.
- relevante Add-on-/Custom-Component-Versionen.
- Backup-/Restore-Status.
- ob Recorder-Konfiguration heute unverändert ist.

## OI-003 – Zentralen Netzfluss physisch gegenprüfen

Priorität: **HOCH**

Durch Konfiguration geklärt:

- Entity: `sensor.shelly_3em_netto_leistung`.
- Einheit: W.
- Config-Semantik: positiv Bezug, negativ Einspeisung.
- Quelle: Summe der drei Shelly-Pro-3EM-Phasen.

Noch offen:

- physischer AT-02-Gegencheck der CT-/Vorzeichenrichtung.
- reales Updateintervall.
- maximale Freshness für die 15-s-Regelschleife.

## OI-004 – Live-Entity-Registry gegen Snapshot diffen

Priorität: **HOCH**

Die relevante Entity-Inventur aus `configuration.yaml` vom 31.08. ist jetzt detailliert dokumentiert.

Noch offen ist ein aktueller HA-Export, um zu unterscheiden:

- vorhanden und online.
- vorhanden aber unavailable.
- disabled/hidden.
- verwaiste Config-/Template-Pfade.
- neue Entities seit 31.08.

Besonders prüfen:

- HMJ-2/B2500-D.
- Bluetti Balco PV.
- Garage Hinten.
- Cool Stash.

## OI-005 – 2400-AC-Live-YAML gegen Snapshot 27.08. diffen

Priorität: **HOCH**

Der vollständige Stand vom 27.08. ist jetzt inventarisiert, inklusive:

- Trigger.
- Aktor-IDs.
- Sollwertgleichung.
- 30-s-Mittelwert.
- Venus-Headroom.
- Failsafes.
- Rampen/Deadbands.

Noch offen:

- ist dies **heute** exakt die produktive Automation?
- gab es nach 27.08. Änderungen?

Vor der nächsten Änderung immer Live-YAML lesen.

## OI-006 – SolarFlow-800-Plus-Automation vollständig importieren

Priorität: **HOCH**

Mehrfache File-Library-Suche hat keine vollständige aktuelle YAML geliefert.

Bekannt ist die Funktionslogik, unbekannt bleiben insbesondere:

- exakte Aktor-Entity-IDs.
- Triggerfrequenzen.
- Deadbands/Rampen.
- Übergangslogik.
- `mode`.
- Freshness-/Failsafe-Prüfungen.

Direkter Export aus HA erforderlich.

## OI-007 – Venus-D-Freshness statt nur Availability

Priorität: **HOCH**

Der letzte 2400-AC-Regler behandelt `unknown`/`unavailable` korrekt konservativ, prüft aber per `has_value()` nur Verfügbarkeit.

Zu klären/testen:

- reales nominales HAME-Updateintervall.
- welcher HA-Zeitstempel zuverlässig die Datenfrische repräsentiert.
- maximal toleriertes Alter für SOC und Venus-Power.
- Failsafe bei numerischem, aber stale State.

Acceptance Tests: AT-03, AT-08, AT-09, AT-11.

## OI-008 – HAME Relay / hm2mqtt Steuerfähigkeit vollständig klären

Priorität: **MITTEL**

Getrennt erfassen:

- Read-Funktionen.
- Write-Funktionen.
- reale Updatefrequenz.
- Schreib-Latenz.
- konkrete Topics/Services nur aus verifizierter Quelle.
- Offline-/Reconnect-Verhalten.
- Restore-/Cache-Verhalten.

## OI-009 – Modbus-RS485 für Venus D bewerten

Priorität: MITTEL

Nur weiterverfolgen, wenn direkter stabiler Steuerzugriff benötigt wird und hm2mqtt nicht genügt.

Keine Registeradressen/Datentypen raten.

## OI-010 – PV-Modul-/WR-Zuordnung inventarisieren

Priorität: MITTEL

Bekannt:

- 29 Module.
- ungefähr 8–8,5 kWp.
- HM-1500-4T.
- HMS-800W-2T.
- HMS-2000-4T.
- SMA ca. 4,5 kW.

Offen:

- Modulmodelle/Wp.
- exakte Modulanzahl je WR/MPPT/String.
- Ausrichtungen.
- physische Standorte.
- aktuelle WR-Limits.

## OI-011 – Klima-Datenpipeline umsetzen

Priorität: **MITTEL**

Bereits geklärt:

- HA Recorder nutzt im Snapshot SQLite.
- 30 Tage Detail-Retention.
- 1-min-Auflösung ist als Ziel ausreichend.

Noch nötig:

1. Privacy-Entscheidung.
2. Live-Bestätigung der Recorder-Konfiguration.
3. Klima-Sensor-Whitelist.
4. Exportmechanismus API vs. read-only DB-Pull.
5. Git-Retention/Aggregation.

## OI-012 – AC Infinity Integration prüfen

Priorität: MITTEL

Benötigt:

- tatsächlich vorhandene AC-Infinity-Modelle.
- aktuelle Integrationsmöglichkeit.
- lokal vs. Cloud.
- Read-/Write-Fähigkeit.
- Updateintervall.

## OI-013 – Energy-Dashboard-Konfiguration vollständig inventarisieren

Priorität: **MITTEL**

Template-Semantik ist jetzt weitgehend dokumentiert. Noch fehlt die tatsächliche Dashboard-Konfiguration:

- welche Entity ist als Netzbezug eingetragen?
- welche als Einspeisung?
- welche PV-Erzeuger?
- welche Batteriesystem-Power-Entities mit Standard/Inverted?
- welche Einzelverbraucher?
- Doppelzählungen?
- verwaiste alte Zähler?

## OI-014 – Messpunkt „Garage Hinten“ physisch definieren

Priorität: **MITTEL**

Entity- und Berechnungsstruktur ist vollständig identifiziert, aber der reale Scope ist nicht sauber dokumentiert:

- nur PV-Unterverteilung?
- allgemeine Garagen-Unterverteilung?
- welche Erzeuger/Verbraucher liegen dahinter?

Für jede spätere Energiebilanz muss das geklärt sein.

## OI-015 – Jupiter-/HMJ-2-Availability härten, falls regelrelevant

Priorität: MITTEL

Aktuelle Templates verwenden bei Teilwerten `float(0)` und prüfen Availability nicht für alle mathematisch notwendigen Eingänge.

Das ist für reine Anzeige tolerierbar, solange bekannt; für Steuerung nicht ausreichend.

Vor Control-Nutzung:

- alle Quellsensoren in Availability.
- Freshness definieren.
- keine stillen Nullwerte.

## OI-016 – Verwendung der Last-Value-Gesamt-SOC-Sensoren auditieren

Priorität: **HOCH**, falls sie in Automationen referenziert werden; sonst MITTEL.

`sensor.verbleibende_energie_bis_mindest_soc` darf absichtlich alte Einzel-SOCs weiterverwenden.

Zu prüfen:

- referenziert irgendeine Aktor-Automation diesen Sensor oder `sensor.gesamt_soc_hausspeicher_nutzbar`?
- falls ja: wird `daten_vollstaendig_aktuell` berücksichtigt?

Ziel: Display-Fallback darf niemals den Venus-D-Control-Failsafe umgehen.

## OI-017 – Zendure Control Ownership live prüfen

Priorität: **HOCH**

Je SF2400 und SF800 prüfen:

- Gerät in HEMS eingebunden?
- Power Manager aktiv?
- App-Zeitpläne aktiv?
- lokale HA-Automation aktiv?
- schreiben mehrere Automationen auf dieselben Limits?

Nur koordinierte Regler zulassen.

## OI-018 – Klima-Entity-Inventar

Priorität: MITTEL

SwitchBot und Govee sind als Sensorquellen bekannt, konkrete Daten fehlen:

- Gerätezahl/Modelle.
- Entity-IDs Temperatur/Feuchte.
- Raum/Position.
- Updateintervall.
- Batterie/Availability.
- Kalibrierung/Offsets.

Erforderlich vor produktiver Klima-Datenpipeline.

## OI-019 – Historische/sekundäre Gerätepfade bereinigen oder kennzeichnen

Priorität: NIEDRIG/MITTEL

Nach Live-Abgleich entscheiden, ob noch benötigte oder verwaiste Pfade vorliegen für:

- HMJ-2/B2500-D.
- Bluetti Balco PV.
- CL Test Echo.
- ggf. alte Entity-Namen aus früheren Shelly-Energiepfaden.

Nicht vor Live-Prüfung löschen.