# Project Timeline

Stand: 2026-09-02

Chronologie tatsächlich bekannter Projekt-Ereignisse. Konfigurationsfunde werden mit ihrem Evidenzdatum geführt und nicht rückwirkend als Live-State umetikettiert.

## 2026-09-02 – Deep Inventory

- Vollständige Detailinventur des bisher verfügbaren Smart-Home-/PV-Projektmaterials durchgeführt.
- GitHub-Register auf Basis des letzten `configuration.yaml`-Snapshots vom 2026-08-31 und des letzten vollständigen SolarFlow-2400-AC-Automationssnapshots vom 2026-08-27 erweitert.
- `DEVICE_REGISTER.md`, `ENTITY_REGISTER.md`, `INTEGRATION_REGISTER.md`, `AUTOMATION_REGISTER.md`, `SYSTEM_CONTEXT.md`, `CURRENT_SYSTEM_STATUS.md`, `ENERGY_FLOW_MODEL.md`, `DATA_PIPELINE.md` und `OPEN_ITEMS.md` reconciled/erweitert.
- klare Evidenztrennung eingeführt: `CONFIGURED SNAPSHOT`, `REPORTED ACTIVE`, `LATEST UPLOADED YAML`, `LIVE VERIFY`.
- zentralen Netzsensor identifiziert: `sensor.shelly_3em_netto_leistung`, Summe dreier Shelly-Pro-3EM-Phasen.
- Konfigurationsvorzeichen dokumentiert: positiv = Netzbezug, negativ = Netzeinspeisung.
- 30-s-Mittelwert `sensor.shelly_3em_netto_leistung_30s_mittel` identifiziert.
- festgestellt: 2400-AC-Regler verwendet Roh-Netzleistung für schnelle Abregelung und Rohwert + 30-s-Mittel gemeinsam zur Hochregelung.
- kompletten 2400-AC-Regelalgorithmus vom Snapshot 27.08. inventarisiert: 15-s-Tagestick, 5-min-Nachttick, 200-W-Rest-Einspeisung, 80-W-Soft-Minimum, 1600-W-Input-Cap in der Automation, +500-W-Aufwärtsrampe, Venus-Headroom und -100-W-Entladeblock.
- bestätigten Failsafe im 2400-AC-Snapshot dokumentiert: fehlt Venus-SOC oder Venus-Power, werden konservativ 2200 W Headroom reserviert; kein letzter Venus-SOC als Live-Steuerwert.
- neuer Blindspot dokumentiert: `has_value()` prüft Availability, aber keine explizite zeitliche Freshness eines weiterhin numerischen HAME-States.
- SolarFlow-800-Plus-YAML erneut in der File Library gesucht; keine vollständige aktuelle Automation gefunden. Funktionsbeschreibung bleibt belegt, exakter YAML-Import offen.
- aktuelles konfiguriertes 4-Speicher-SOC-Modell identifiziert: SF800 Plus 1,92 kWh + SF2400 AC 2,88 kWh + Venus D 5,12 kWh + Jupiter C Plus 5,12 kWh = 15,04 kWh Nennkapazität.
- im Gesamt-SOC-Template definierte nutzbare Energie 12,8512 kWh und Reserve 2,1888 kWh dokumentiert.
- erkannt: `sensor.verbleibende_energie_bis_mindest_soc` verwendet bei Ausfall letzte gültige SOC-Werte und kennzeichnet dies über Attribute. Als Dashboard-/Analysepfad dokumentiert, nicht automatisch als Live-Control-Sensor.
- Jupiter-C-Plus-HAME-Pfade inkl. vier PV-Eingänge, Combined Power und SOC inventarisiert.
- HMJ-2/B2500-D-System-Power-Pfad im Config-Snapshot identifiziert, aber wegen fehlender aktueller Live-Rolle als sekundär/VERIFY klassifiziert.
- „Bluetti Balco PV“-Shelly-Pfad identifiziert, physische Zuordnung zum heutigen Bluetti-Setup bleibt VERIFY.
- zentralen zusätzlichen Shelly 3EM Gen3 „Garage Hinten“ vollständig inventarisiert: Phasen-Netto/Bezug/Einspeisung, Gesamtwerte, Connectivity und integrierte kWh.
- wichtige Semantik dokumentiert: Garage-Hinten `Netto Gesamt` ist saldiert; `Bezug Gesamt`/`Einspeisung Gesamt` sind phasenweise Richtungssummen und nicht identisch mit saldiertem Gesamtimport/-export.
- Anzucht-, Klimaanlagen-, Outdoor-, Schuko-Lader-, HMS-800W-2T-, Venus-D- und SF2400-Shelly-Pfade inventarisiert.
- Recorder-Konfiguration aus letztem Snapshot identifiziert: SQLite `/config/home-assistant_v2.db`, 30 Tage Retention, Auto-Purge, Commit-Intervall 30 s.
- Generic Thermostat „Kühlschrank Cool Stash“ inklusive Ziel 17 °C, Sensor-/Aktorpfad und Zyklusparametern inventarisiert.
- detaillierte Live-Datenlücken in `OPEN_ITEMS.md` neu priorisiert.

## 2026-09-02 – Projektinitialisierung

- GitHub-Repository `konraddi/smarthome-pv` als kanonische Projektakte initialisiert.
- Agent-/Governance-Struktur angelegt.
- bekannten PV-/Speicher-/Home-Assistant-Kontext initial inventarisiert.
- Projektgrenze zu `konraddi/Heise-Testberichte` festgelegt.
- Privacy-Risiko des derzeit öffentlichen Repositories als Open Item aufgenommen.
- Venus-D-SOC-Ausfallregel als verbindlicher Failsafe-Grundsatz übernommen.
- Klima-Datenpipeline als eigener Designbereich angelegt.

## 2026-09-01

- Ziel formuliert, Klima-/Feuchtewerte aus Home Assistant regelmäßig in das Projekt zu übernehmen.
- ungefähr 1-Minuten-Auflösung als ausreichend festgelegt.
- SwitchBot- und Govee-Daten als vorhandene HA-Quellen genannt.
- AC-Infinity-Integration bzw. Export als Prüfpunkt identifiziert.

## 2026-08-31

- letzter aktuell vorliegender `configuration.yaml`-Snapshot im Projektmaterial.
- Energiezuordnung des Anzucht-Shelly auf aktuelle Nutzung als Verbrauch/`energy_consumed` ausgerichtet.
- Outdoor Plug mit festen hardwareunabhängigen Verbrauchs-/Einspeise-/Power-/Strom-/Spannungs-/Frequenz-Templates ergänzt.
- konfigurierter Gesamt-SOC der vier Hausspeicher und 30-s-Nettofilter im Snapshot vorhanden.

## 2026-08-27

- letzter vollständig wiedergefundener YAML-Snapshot der Automation `Zendure SolarFlow 2400 AC PV-Überschussladen und Nachtentladung`.
- dort Venus-D-Live-SOC-/Power-Failsafe ohne Last-Value-SOC implementiert.

## 2026-08 – Venus-D-/SolarFlow-Failsafe

- Venus-D-Status/SOC war nicht abrufbar bzw. der SOC über längere Zeit ausgefallen.
- Beobachtung: SolarFlow 2400 AC lud in dieser Situation nicht korrekt; Teile des Überschusses gingen ins Netz.
- Entscheidung: für die Regelung nicht den letzten bekannten Venus-D-SOC weiterverwenden, sondern einen expliziten Failsafe verwenden.

## 2026-07 – SolarFlow 800 Plus Automation

- Automation `Zendure SolarFlow 800 Plus AC-Zusatzladung und Nachtentladung` im Projekt iteriert.
- bekannte Logik: Zusatz-AC-Ladung tagsüber, Nachtentladung, Rest-Einspeisungsziel und Koordination mit Venus-D-Verhalten.

## 2026-06/07 – SolarFlow 2400 AC Automation

- Home-Assistant-Steuerung für Tages-/Nachtbetrieb und Überschussmanagement iteriert.
- Rest-Einspeisung statt harter Nullregelung als praktische Leitlinie genutzt.

## Historischer Bestand

- PV-Anlage mit 29 Modulen und ungefähr 8–8,5 kWp im Betrieb.
- Hoymiles HM-1500-4T, HMS-800W-2T, HMS-2000-4T und SMA-Stringwechselrichter im Projektkontext.
- Shelly Pro 3EM als zentraler Messpfad.
- mehrere Speicher-/Energiesysteme im Projektkontext, darunter Zendure, Marstek, Jackery und Bluetti.