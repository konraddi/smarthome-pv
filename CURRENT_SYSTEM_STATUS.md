# Current System Status

Stand: 2026-09-02

Diese Datei bildet den aktuell bekannten technischen Ist-Zustand ab. Unbekannte oder nicht frisch verifizierte Punkte bleiben ausdrücklich `UNKNOWN`.

## 1. Home Assistant

Status: `ACTIVE / REPORTED`

Bekannt:

- Home Assistant ist aktiv im Einsatz.
- PV-/Speicher-/Shelly-Automationen laufen produktiv.
- Energy-Dashboard wird genutzt.
- Klima-/Feuchtesensoren sind eingebunden.

Noch nicht kanonisch inventarisiert:

- exakte HA-Version
- Recorder-/DB-Konfiguration
- vollständige Entity-Liste
- vollständige Automations-YAML
- Backup-/Restore-Stand

## 2. Zentraler Netzfluss

Messpfad: Shelly Pro 3EM.

Status der exakten kanonischen Netto-Entity und Vorzeichenkonvention: `VERIFY`.

Bekannt ist, dass die Regelungen eine saldierende Haus-/Netzbilanz verwenden sollen. Eine kleine Rest-Einspeisung von ungefähr 200 W wird in mehreren Speicherlogiken bewusst als Regelziel verwendet.

## 3. SolarFlow 2400 AC

Status: `ACTIVE`.

Bekannte Betriebslogik aus dem aktuellen Projektstand:

- tagsüber ungefähr 08:00–20:00 Uhr Nutzung im Lade-/Input-Kontext
- nachts ungefähr 20:00–08:00 Uhr Entladung
- Nachtleistung im Projektverlauf etwa 120–200 W je nach Automationsstand
- Mindest-SOC etwa 10 %
- Regelziel tagsüber teilweise ungefähr 200 W Rest-Netzeinspeisung

Wichtiger aktueller Failsafe-Befund:

- Der Marstek-Venus-D-SOC war über längere Zeit nicht verfügbar.
- Dadurch durfte der SolarFlow 2400 AC nicht weiter auf einem alten Venus-D-SOC planen.
- Projektentscheidung: bei fehlendem Live-SOC **nicht** letzten SOC weiterverwenden; sichere Ausfalllogik anwenden.

Exakte aktuelle YAML: `IMPORT REQUIRED`.

## 4. SolarFlow 800 Plus

Status: `ACTIVE`.

Bekannte Automationslogik:

- Alias: `Zendure SolarFlow 800 Plus AC-Zusatzladung und Nachtentladung`
- 11:00–17:00 Uhr zusätzliche AC-Ladung bis max. ungefähr 800 W
- Freigabe u. a. wenn SolarFlow 2400 AC über ungefähr 89 % SOC liegt
- dabei ungefähr 200 W Rest-Netzeinspeisung am Shelly anstreben
- AC-Zusatzladung pausieren, wenn der Venus D/Marstek tagsüber über seinen Shelly spürbar entlädt
- tagsüber `outputLimit 0` für PV-Passthrough
- nachts 20:00–08:00 Uhr Entladung mit ungefähr 120 W
- nachts regelmäßige Prüfung, ob Modus, Input und Output noch korrekt stehen

Exakte aktuelle YAML und Übergangsfenster: `IMPORT REQUIRED`.

## 5. Marstek Venus D

Status: `ACTIVE / PARTIAL TELEMETRY`.

- HAME Relay + hm2mqtt
- Updateintervall ungefähr 1 Minute
- SOC-Pfad war mindestens zeitweise über längeren Zeitraum nicht verfügbar
- letzter SOC darf nicht als aktueller Steuerwert fortgeschrieben werden
- zuverlässiger Write-/Control-Pfad: `UNKNOWN / VERIFY`

## 6. Weitere Energiesysteme

Bekannt im Projekt:

- Jackery 2000 Ultra
- Bluetti Elite 300 + Transfer Hub
- Marstek Venus E Mini

Aktiver HA-Steuerstatus dieser Geräte: `UNKNOWN`, sofern nicht separat verifiziert.

## 7. Shelly / Energy Dashboard

Zuletzt verifiziertes Projekt-Learning:

- Ein Shelly an der Anzucht war früher einmal für einen Mikrowechselrichter genutzt worden.
- Seit Nutzung an der Anzucht wurde nur noch Strom bezogen; die vorhandenen Energy-Werte waren deshalb als Verbrauch plausibel.
- Die Zuordnung wurde auf **consumed** statt erzeugt/produced umgestellt.
- Der Outdoor Plug wurde ebenfalls in die Verbrauchslogik aufgenommen.

Exakte aktuelle Entity-IDs werden noch aus HA importiert.

## 8. Klima-Daten

Status: `ACTIVE IN HA / GITHUB PIPELINE NOT YET IMPLEMENTED`.

Bekannte Quellen:

- SwitchBot Thermo-Hygrometer
- Govee

Ziel:

- täglicher Export
- ungefähr 1-Minuten-Auflösung ausreichend
- Graph-/Verlaufsanalyse im Projekt

Privacy-Gate wegen öffentlichem Repository beachten.

## 9. Aktuelle Prioritäten

1. vollständige aktuelle HA-Entity-/Automationsinventur importieren
2. zentrale Netzleistungs-Entity inklusive Vorzeichen verifizieren
3. aktuelle 2400-AC-YAML kanonisch übernehmen
4. aktuelle 800-Plus-YAML kanonisch übernehmen
5. Venus-D-SOC-/Freshness-Failsafe explizit in Automation und Tests festhalten
6. Repo-Privacy entscheiden, bevor detaillierte Klima-/Anwesenheitszeitreihen exportiert werden