# Automation Register

Stand: 2026-09-02

Dieses Register ist Index und Wirkungsübersicht. Die vollständige aktuelle YAML wird später kontrolliert aus Home Assistant übernommen und nicht aus Chat-Erinnerung rekonstruiert.

## AUTO-ENERGY-001 – SolarFlow 2400 AC Hausenergie-Regelung

Status: `ACTIVE / YAML IMPORT REQUIRED`

Zweck:

- tagsüber Überschuss-/Laderegelung
- nachts definierte Grundentladung
- Koordination mit weiteren Speichern

Bekannte Parameter/Leitlinien:

- tagsüber ungefähr 08:00–20:00 Uhr Input-/Ladekontext
- nachts ungefähr 20:00–08:00 Uhr Entladung
- Nachtleistung im Projektverlauf etwa 120–200 W
- Mindest-SOC ungefähr 10 %
- tagsüber teilweise Ziel von ungefähr 200 W Rest-Netzeinspeisung

Bekannte kritische Inputs:

- zentrale Shelly-Nettoleistung
- SolarFlow-2400-AC-SOC
- Venus-D-Status/SOC bzw. daraus abgeleitete Sperrlogik

Verbindlicher Failsafe:

- wenn Venus-D-Live-SOC nicht verfügbar/zu alt ist, **nicht** letzten SOC als aktuell weiterverwenden.
- Ausfallstatus muss zu einer bewusst definierten sicheren Regelreaktion führen.

Offen:

- exakte Alias-/Automation-ID
- vollständige YAML
- exakte Freshness-Schwellen
- exakte Nachtleistung des aktuellen Stands
- exakte Service-/MQTT-Aktoren

## AUTO-ENERGY-002 – Zendure SolarFlow 800 Plus AC-Zusatzladung und Nachtentladung

Status: `ACTIVE / YAML IMPORT REQUIRED`

Bekannter Alias:

`Zendure SolarFlow 800 Plus AC-Zusatzladung und Nachtentladung`

Bekannte Logik:

- 11:00–17:00 Uhr zusätzliche AC-Ladung
- bis ungefähr 800 W zusätzliche Ladeleistung
- Freigabe u. a. bei SolarFlow 2400 AC > ungefähr 89 % SOC
- ungefähr 200 W Rest-Netzeinspeisung am Shelly anstreben
- AC-Zusatzladung pausieren, wenn Venus D/Marstek über seinen Shelly tagsüber spürbar entlädt
- tagsüber `outputLimit 0` für PV-Passthrough
- nachts 20:00–08:00 Uhr ungefähr 120 W Entladung
- nachts regelmäßige Prüfung, ob Modus, Input und Output weiterhin korrekt gesetzt sind

Kritische Inputs:

- zentrale Shelly-Nettoleistung
- 2400-AC-SOC
- Venus-D-/Marstek-Leistungszustand
- SolarFlow-800-Plus-SOC/Modus

Offen:

- vollständige aktuelle YAML
- Übergangsfenster rund um 08:00/20:00
- exakte Check-Frequenzen und Services
- Freshness-Regeln je Input

## AUTO-ENERGY-003 – Energy-Dashboard Verbrauchszuordnung Anzucht/Outdoor

Typ: Konfigurations-/Messlogik, keine klassische Regelautomation.

Status: `IMPLEMENTED / REPORTED`

Bekannte Entscheidung:

- Anzucht-Shelly zählt in aktueller Nutzung Verbrauch und wird als `consumed` behandelt.
- Outdoor Plug ebenfalls als Verbrauch einbezogen.

## Registerpflicht je Automation

Bei Import der echten YAML ergänzen:

- Automation-ID / Alias
- kanonischer Dateipfad
- Trigger
- Conditions
- Aktionen/Stellgrößen
- kritische Inputs
- Control Owner
- nominale Update-/Regelrate
- Freshness-Schwellen
- Failsafe je kritischem Input
- manueller Override
- Race-Condition-/Parallelitätsrisiko
- letzte Änderung
- Acceptance-Test-Status

## Regel

Eine Automation darf nicht aufgrund eines älteren Chatstands als „aktuell“ dokumentiert werden. Die reale HA-YAML hat Vorrang und muss vor Änderung importiert bzw. direkt gelesen werden.