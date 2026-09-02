# System Context

Stand: 2026-09-02

Diese Datei enthält stabile bzw. langsam veränderliche Rahmenbedingungen. Live-Zustände gehören nach `CURRENT_SYSTEM_STATUS.md`. Konfigurationssnapshots beweisen Konfiguration, nicht automatisch heutige Online-Zustände.

## 1. Standort und Netzmodell

- Standort: Bayern, Deutschland.
- Haus-/Anlagenbetrachtung erfolgt über einen **saldierenden Zähler**.
- Für Regelungen auf Haus-/Netzebene ist die Gesamtbilanz entscheidend, nicht die isolierte Leistung einer einzelnen Phase.
- Kanonischer HA-Netto-Pfad ist als Summe der drei Phasen des zentralen Shelly Pro 3EM konfiguriert.
- Konfigurationsvorzeichen: **positiv = Netzbezug, negativ = Netzeinspeisung**.

## 2. Home Assistant – Architektur

Home Assistant ist die zentrale Beobachtungs- und Automationsplattform.

Bekannte Aufgaben:

- PV-/Netzleistungsbewertung.
- Speichersteuerung.
- Shelly-Energieerfassung.
- Zendure-Steuerung.
- Marstek-/HAME-/MQTT-Monitoring.
- Klima-/Temperatur-/Feuchteerfassung.
- Energy-Dashboard.
- Generic-Thermostat-Regelung.
- 30-s-Leistungsfilter.
- Integration von Leistung nach Energie für Unterzähler.

Konfigurationsstruktur im letzten Snapshot:

- `default_config`.
- Themes aus separatem Theme-Ordner.
- `automation: !include automations.yaml`.
- `script: !include scripts.yaml`.
- `scene: !include scenes.yaml`.
- umfangreiche Template-Sensorik.
- `filter`-Sensor für 30-s-Nettoleistung.
- `integration`-Sensoren für Garage-Hinten-kWh.
- `generic_thermostat` für „Kühlschrank Cool Stash“.
- `command_line`-Diagnosesensoren.

Exakte Home-Assistant-Core-Version und Installationsart: `UNKNOWN / LIVE VERIFY`.

## 3. Recorder / Historie

Letzter vorliegender Config-Snapshot vom 2026-08-31:

- Datenbank: SQLite.
- Pfad: `/config/home-assistant_v2.db`.
- Detail-Retention: 30 Tage.
- `auto_purge: true`.
- `commit_interval: 30` Sekunden.
- eingeschlossene Domains: `sensor`, `switch`, `energy`, `binary_sensor`.
- ausgeschlossene Domains: `updater`, `persistent_notification`, `script`.

Zusätzlich ist ein Command-Line-Sensor zur Datenbankgröße konfiguriert.

Dieser Stand ist `DOCUMENTED / CONFIG SNAPSHOT`, nicht heute live aus HA gelesen.

Backup-/Restore-Konzept: `UNKNOWN`.

## 4. PV-Bestand und Topologie

### 4.1 Projektweiter Bestandsrahmen

Im aktuellen Projektkontext als Bestand geführt:

- 29 PV-Module.
- insgesamt ungefähr 8–8,5 kWp.
- Hoymiles HM-1500-Familie.
- Hoymiles HMS-800W-2T.
- Hoymiles HMS-2000-4T.
- SMA-Stringwechselrichter, ca. 4,5 kW.

Für den HMS-800W-2T existiert ein eigener Shelly-PV-Messpfad in HA.

### 4.2 Datiertes Topologie-Snapshot 2026-07-29

Vom Nutzer damals konkret als physische Topologie beschrieben:

- **vordere Garage:** 2× Hoymiles HM1500; je Wechselrichter 2× JA-Solar-Module mit 440 Wp.
- **hintere Garage:** 1× Hoymiles HMS2000; 4× 440-Wp-Module.
- **Balkon:** 1× Hoymiles HMS800; 2× 440-Wp-Module; Ausrichtung Westen.

Status: `REPORTED 2026-07-29 / HISTORICAL TOPOLOGY SNAPSHOT / LIVE VERIFY`.

### 4.3 Datiertes „Winterkonfiguration“-Snapshot 2026-07-30

Am Folgetag wurde ausdrücklich eine **Winterkonfiguration** beschrieben:

- **vordere Garage:** 2× Hoymiles HM1500; je 3 Module.
- **hintere Garage:** 2× Hoymiles HM1500; je 4×430 W, zusätzlich 1× Hoymiles HMS2000 mit 4×500 W.
- **Balkon:** 2×430 W, vertikal/90°, Westausrichtung; im Winter als vernachlässigbar beschrieben.
- **übrige Module:** südausgerichtet, flache ballastierte Aufständerung.
- für diese Winterkonfiguration wurde ausdrücklich **kein SMA-Stringwechselrichter** genannt bzw. „kein SMA“ angegeben.

Status: `REPORTED 2026-07-30 / WINTER CONFIGURATION / NOT AUTOMATICALLY CURRENT LIVE TOPOLOGY`.

### 4.4 Konflikt-/Zeitlogik

Die Angaben vom 29.07. und 30.07. unterscheiden sich erheblich bei Modulzahl, Modulleistung und Zahl der HM1500. Zusätzlich steht die Winterkonfiguration mit „kein SMA“ im Konflikt zum später im Projektbestand geführten SMA-Stringwechselrichter.

Daher gilt **kein stilles Zusammenführen**. Möglich sind u. a. Plan-/Umbauzustand, saisonale Rekonfiguration oder spätere Erweiterung; die Ursache wird nicht erfunden.

Kanonischer heutiger physischer Stand: `LIVE INVENTORY REQUIRED`.

Vor endgültiger Zuordnung sind zu erfassen:

- Anzahl der heute tatsächlich montierten Wechselrichter je Modell.
- Modulzahl und Wp je WR/MPPT/String.
- Standort/Ausrichtung je Modulgruppe.
- aktueller SMA-Status und dessen Strings.
- welche Teile des 30.07.-Snapshots Plan/Winterziel und welche tatsächlich umgesetzt sind.

## 5. Netz- und Unterverteilungsmessung

### Zentraler Netzanschlusspunkt

Shelly Pro 3EM, Rohpfade:

- Phase A/B/C Active Power.
- daraus Template `sensor.shelly_3em_netto_leistung`.
- zusätzliche Import-/Export-Anzeige.
- zusätzlicher 30-s-Mittelwert.

Für Haus-/Speicherregelung ist der **saldierte Netto-Sensor** autoritativ.

### Garage Hinten

Separater Shelly 3EM Gen3 mit drei Phasen und eigenen Templates für:

- Netto je Phase.
- positive Bezugsanteile je Phase.
- negative Einspeiseanteile je Phase.
- Netto gesamt.
- Summe der Bezugsanteile.
- Summe der Einspeiseanteile.
- integrierte kWh je Phase und gesamt.

Wichtig: Summe positiver Bezugsanteile und Summe negativer Einspeiseanteile sind bei gemischten Phasenrichtungen **nicht** die saldierte Haus-/Netzbilanz. Exakter physischer Stromkreis „Garage Hinten“ noch `VERIFY`.

## 6. Aktuell konfiguriertes 4-Speicher-Modell

Der letzte HA-Config-Snapshot enthält einen kapazitätsgewichteten Gesamt-SOC für genau vier Speicher:

| Speicher | Nennkapazität | im Template verwendeter Nutzbereich |
|---|---:|---|
| Zendure SolarFlow 800 Plus | 1,92 kWh | 10–90 % |
| Zendure SolarFlow 2400 AC | 2,88 kWh | 10–90 % |
| Marstek Venus D | 5,12 kWh | 12–100 % |
| Marstek Jupiter C Plus | 5,12 kWh | 12–100 % |

Gesamt:

- Nennkapazität: **15,04 kWh**.
- im Template maximal nutzbare Energie: **12,8512 kWh**.
- Reserve außerhalb der definierten Nutzbereiche: **2,1888 kWh**.

Dieses Modell ist die aktuelle **Konfigurationsstruktur**, keine Aussage über heutigen SOC oder Online-Status.

### Historische AC-Kopplungsangabe 2026-07-30

Für die damalige Winterkonfiguration wurden folgende drei Speicher ausdrücklich als **rein AC-gekoppelt und ohne direkt angeschlossene PV-Module** beschrieben:

- Marstek Venus D, damals ca. 5 kWh / zwei Einheiten beschrieben.
- Zendure SolarFlow 2400 AC mit einer Batterie, ca. 2,8 kWh.
- Zendure SolarFlow 800 Plus mit einer Batterie, ca. 1,9 kWh.

Das ist ein `HISTORICAL/WINTER CONFIG SNAPSHOT`; heutige PV-Direktanbindung jedes Speichers ist separat zu verifizieren.

## 7. Weitere Speicher-/Energiesysteme

Im Projektbestand bekannt:

- Jackery 2000 Ultra.
- Bluetti Elite 300 + Transfer Hub.
- Marstek Venus E Mini.

Zusätzlich im letzten HA-Config-Snapshot vorhanden:

- Marstek B2500-D / HAME `HMJ-2` System-Power-Template.
- Messpfad mit Template-Namen `Bluetti Balco PV`.

Historisch zusätzlich konkret eingebunden:

- Hoymiles HiBattery 1920 AC / MS-A2 über HA/MQTT.
- OpenDTU als Hoymiles-PV-Telemetriepfad.

Diese historischen/sekundären Pfade werden **nicht** automatisch als aktive Mitglieder der aktuellen Speicherregelung behandelt.

## 8. Zendure-Regelarchitektur

### SolarFlow 2400 AC

Aktiv vom Nutzer berichtet. Letzter vollständiger Automationssnapshot 2026-08-27.

Grundprinzip:

- Tag 08:00–20:00: `Input mode`, dynamische AC-Überschussladung.
- Nacht 20:00–08:00: `Output mode` und 190 W Output im letzten Snapshot.
- Tagesziel: ungefähr 200 W Rest-Netzeinspeisung.
- Soft-Minimum 80 W.
- max. Tages-Input in der Automation 1600 W.
- Hochregelung max. +500 W je Regeldurchlauf.
- Roh-Netzleistung für schnelle Reduktion.
- Rohwert + 30-s-Mittel gemeinsam zur Hochregelung.
- Venus D erhält unter 90 % SOC Lade-Headroom.
- fehlen Venus-SOC oder Venus-Leistung: volle 2200-W-Headroom-Reserve als Failsafe.
- Venus D entlädt stärker als 100 W: SolarFlow auf 80 W zurück.

Diese Werte sind **Automationsparameter des Snapshots**, nicht automatisch Geräte-Nennwerte.

### SolarFlow 800 Plus

Aktive HA-Automation vom Nutzer berichtet; vollständige aktuelle YAML fehlt noch.

Bekanntes Verhalten:

- Zusatz-AC-Ladung 11:00–17:00.
- ungefähr bis 800 W.
- Nutzung 2400-AC-SOC/Marstek-Zustand.
- ca. 200 W Rest-Einspeisungsziel.
- tagsüber Output-Limit 0 für PV-Passthrough.
- Nachtentladung ungefähr 120 W im letzten bekannten Funktionsstand.

## 9. Marstek Venus D

Bekannt:

- Anbindung über HAME Relay + hm2mqtt.
- Telemetrie ungefähr einmal pro Minute.
- SOC-Pfad kann länger ausfallen.
- SolarFlow 2400 AC darf bei fehlendem Venus-Live-SOC nicht mit einem alten Venus-SOC weiterplanen.
- in der letzten 2400-AC-Automation wird bei ungültigem Venus-SOC/Power konservativ volle Venus-Headroom reserviert.

Write-/Control-Fähigkeit über HAME/hm2mqtt: `VERIFY`.  
Modbus-RS485 als möglicher alternativer Steuerpfad: `DISCUSSED / NOT VERIFIED`.

## 10. SOC-Aggregation und Datenqualität

`Verbleibende Energie bis Mindest-SoC` speichert letzte gültige SOC-Werte und verwendet sie weiter, wenn eine Live-Quelle vorübergehend fehlt. Dazu existieren Datenqualitätsattribute, u. a. `daten_vollstaendig_aktuell` und `quellen_mit_letztem_wert`.

Projektregel:

- Fallback ist für **Dashboard/Trend** zulässig.
- Fallback ist **kein Live-Regelwert**.
- Aktor-Automationen dürfen den bekannten Venus-D-Failsafe nicht über den Aggregatsensor umgehen.

## 11. Klima / Thermostat

Home Assistant sammelt Temperatur-/Feuchtedaten u. a. aus:

- SwitchBot Thermo-Hygrometern.
- Govee-Sensoren.

Konkrete Klima-Entity-Inventur: `OPEN / LIVE EXPORT REQUIRED`.

Zusätzlich konfiguriert:

- Generic Thermostat „Kühlschrank Cool Stash“.
- Aktor: Shelly Plug S Gen 3.
- Temperatursensor `cool_stash_temperature`.
- Ziel 17 °C.

Zusätzlich aus einem früheren HA-State-Snapshot bekannt:

- `sensor.wifi_thermometer` / `Thermometer Grow`, damals `unavailable`; physisches Modell/Integration noch `UNKNOWN`.

## 12. Security-/Privacy-Rahmen

Das Repository `konraddi/smarthome-pv` ist zum Initialisierungszeitpunkt öffentlich.

Daher nicht persistieren:

- Home-Assistant-/MQTT-Passwörter/Tokens.
- private IP-Adressen.
- externe Zugangsdaten/URLs, wenn nicht zwingend nötig.
- WLAN-Credentials/API-Keys.
- Private Keys.
- detaillierte Klima-/Anwesenheitszeitreihen ohne bewusste Privacy-Entscheidung.

Technisch notwendige Entity-IDs werden dokumentiert; sie sind keine Zugangsdaten, werden aber nicht unnötig um Netzwerktopologie/Serieninformationen erweitert.