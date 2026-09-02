# System Context

Stand: 2026-09-02

Diese Datei enthält stabile bzw. langsam veränderliche Rahmenbedingungen. Live-Zustände gehören nach `CURRENT_SYSTEM_STATUS.md`.

## 1. Standort und Netzmodell

- Standort: Bayern, Deutschland.
- Haus-/Anlagenbetrachtung erfolgt über einen **saldierenden Zähler**.
- Für Regelungen auf Haus-/Netzebene ist daher die Gesamtbilanz entscheidend, nicht die isolierte Leistung einer einzelnen Phase.

## 2. Home Assistant

Home Assistant ist die zentrale Beobachtungs- und Automationsplattform des Projekts.

Bekannte Aufgaben:

- PV-/Netzleistungsbewertung
- Speichersteuerung
- Shelly-Energieerfassung
- Zendure-Steuerung
- Marstek-/HAME-/MQTT-Monitoring
- Klima-/Temperatur-/Feuchteerfassung
- Energy-Dashboard

Exakte HA-Version, Datenbanktyp, Recorder-Aufbewahrung und Backupstrategie sind derzeit in dieser Projektakte noch `UNKNOWN` und werden nicht aus Erinnerung ergänzt.

## 3. PV-Bestand

Bekannter Gesamtbestand:

- 29 PV-Module
- insgesamt ungefähr 8–8,5 kWp

Bekannte Wechselrichter:

- Hoymiles HM-1500-4T
- Hoymiles HMS-800W-2T
- Hoymiles HMS-2000-4T
- SMA-Stringwechselrichter, ca. 4,5 kW

Die exakte Modulzuordnung zu Wechselrichtern/Strings und die aktuellen Leistungsgrenzen werden erst nach verifizierter Inventur kanonisch ergänzt.

## 4. Batteriespeicher / Energiesysteme

Bekannte Systeme im Projektkontext:

- Zendure SolarFlow 2400 AC mit AB3000X, 2,88 kWh
- Zendure SolarFlow 800 Plus, integrierter Speicher 1,92 kWh
- Marstek Venus D
- Jackery 2000 Ultra
- Bluetti Elite 300 + Transfer Hub
- Marstek Venus E Mini als Test-/Integrationsgerät

Review-spezifische Messreihen und Claims zum Venus E Mini oder anderen Testgeräten gehören in `konraddi/Heise-Testberichte`; hier nur betriebsrelevante Integration.

## 5. Messung

Bekannte Messgeräte/-pfade:

- Shelly Pro 3EM als zentraler Netz-/Phasenmesspfad
- Shelly Plug S Gen3 an einzelnen Verbrauchern/Erzeugern
- weitere Smart-Plug-/Gerätemessungen je nach Setup

Die kanonische Vorzeichenkonvention des zentralen Netto-Leistungssensors ist noch zu verifizieren und wird in `ENTITY_REGISTER.md` dokumentiert.

## 6. Zendure-Regellogik

Bekannte Projektprinzipien:

- SolarFlow 2400 AC wird in Home Assistant aktiv in die Hausenergieoptimierung einbezogen.
- SolarFlow 800 Plus wird ebenfalls aktiv geregelt.
- Ziel kann bewusst eine kleine Rest-Netzeinspeisung von ungefähr 200 W sein, damit die Regelung nicht ständig zwischen Bezug und Einspeisung kippt.
- HEMS/Herstellerregelung und Home Assistant dürfen nicht unkoordiniert dieselben Limits/Stellgrößen regeln.

Exakte aktuelle YAML und alle Stellgrößen müssen aus den realen Automationen importiert werden; nicht aus Erinnerung rekonstruieren.

## 7. Marstek Venus D

Bekannt:

- Anbindung über **HAME Relay** und **hm2mqtt**.
- Telemetrie-Update ungefähr einmal pro Minute.
- Der SOC-Pfad war zeitweise über längere Zeit nicht verfügbar.
- Der letzte bekannte SOC soll in diesem Fall **nicht** als aktuelle Steuerfreigabe für andere Speicher weiterverwendet werden.

Ob und in welchem Umfang dieser Pfad zuverlässig schreiben/steuern kann, ist separat von der Lesefunktion zu dokumentieren. Ein möglicher Modbus-RS485-Steuerpfad wurde diskutiert, ist aber noch nicht als kanonisch verifiziert dokumentiert.

## 8. Klima-/Sensordaten

Home Assistant sammelt Temperatur-/Feuchtedaten u. a. aus:

- SwitchBot Thermo-Hygrometern
- Govee-Sensoren

Ziel ist eine projektverfügbare, möglichst tägliche Datenübernahme mit ungefähr 1-Minuten-Auflösung für Verlauf und Graphanalyse.

AC-Infinity-Integration bzw. direkter Export ist als offener Integrationspunkt geführt.

## 9. Security-/Privacy-Rahmen

Das Repository `konraddi/smarthome-pv` ist zum Initialisierungszeitpunkt öffentlich.

Daher nicht hier persistieren:

- Home-Assistant-Passwörter/Tokens
- MQTT-Credentials
- externe DDNS-/HA-Zugangsdetails, soweit nicht zwingend nötig
- private IP-Adressen
- WLAN-Zugangsdaten
- API-Keys
- detaillierte Zeitreihen, die Anwesenheitsprofile offenlegen können, ohne bewusste Freigabe/Privacy-Entscheidung.