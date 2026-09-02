# Data Pipeline

Stand: 2026-09-02

Ziel: reproduzierbare Übernahme ausgewählter Home-Assistant-Zeitreihen in eine Form, die im Projekt analysiert und visualisiert werden kann.

## 1. Primärquelle

Home Assistant bzw. dessen Recorder-Datenbank bleibt Primärquelle für Live- und Historienwerte.

Letzter vorliegender HA-Config-Snapshot vom 2026-08-31 dokumentiert:

- Recorder-Datenbank: **SQLite**.
- Pfad: `/config/home-assistant_v2.db`.
- `purge_keep_days: 30`.
- `auto_purge: true`.
- `commit_interval: 30` Sekunden.
- explizit enthaltene Domains: `sensor`, `switch`, `energy`, `binary_sensor`.
- ausgeschlossen: `updater`, `persistent_notification`, `script`.

Status: `DOCUMENTED / CONFIG SNAPSHOT / LIVE VERIFY BEFORE IMPLEMENTATION`.

GitHub-Daten bleiben exportierte Analysesnapshots, keine konkurrierende operative Datenbank.

## 2. Vorhandene DB-Diagnose

Im selben Config-Snapshot ist ein Command-Line-Sensor `HA DB Size` mit unique_id `ha_db_size` konfiguriert.

Er ermittelt ungefähr alle 300 s die Größe von `/config/home-assistant_v2.db` in MB.

Das kann später zur Beurteilung von Recorder-Wachstum/Retention genutzt werden; die heutige Entity-ID und der aktuelle Wert sind noch nicht live gelesen.

## 3. Zielbild Klima

Gewünscht:

- täglicher Export.
- etwa 1-Minuten-Auflösung ausreichend.
- Temperatur und relative Luftfeuchte.
- Quellen u. a. SwitchBot und Govee.
- später ggf. AC Infinity.
- Graph-/Trendbewertung im Projekt.

Konkrete SwitchBot-/Govee-Entity-IDs fehlen weiterhin im Inventar.

## 4. Priorisierte Exportarchitektur

Da SQLite und 30-Tage-Retention dokumentiert sind, bestehen grundsätzlich drei sinnvolle Wege:

### A. Home-Assistant-API Pull

Vorteile:

- entkoppelt vom DB-Schema.
- semantisch näher an HA-Entities.
- keine direkte SQLite-Dateisperre/-Kopierlogik.

Nachteile:

- Token/Authentisierung erforderlich; Secrets niemals in Git.
- Historienabfragen für viele hochauflösende Entities können aufwendig werden.

### B. kontrollierter SQLite-Read

Vorteile:

- vorhandene lokale Daten direkt verfügbar.
- effizient für definierte Zeitfenster.

Risiken:

- HA-Recorder-Schema ist Implementierungsdetail und versionsabhängig.
- niemals die produktive DB durch unkontrollierte Schreibzugriffe verändern.
- für Analyse bevorzugt read-only bzw. Kopie/Snapshot verwenden.

### C. separate Timeseries-Pipeline

z. B. InfluxDB oder anderer Langzeitspeicher, falls 30 Tage Recorder-Retention langfristig zu kurz werden.

Aktuell nicht als erforderlich entschieden.

## 5. Empfohlenes Datenformat nach Privacy-Freigabe

Pfadmodell:

`DATA/CLIMATE/YYYY/MM/YYYY-MM-DD.csv`

Spalten mindestens:

- `timestamp`
- `entity_id`
- `value`
- `unit`
- `quality`

`quality` z. B.:

- `VALID`
- `UNKNOWN`
- `UNAVAILABLE`
- `RESTORED`
- `STALE`
- `GAP`

Keine Lücken still interpolieren.

## 6. Datenqualität / Freshness

Bei Zeitreihen nicht nur den numerischen Wert exportieren, sondern Datenqualität soweit rekonstruierbar erhalten.

Besonders wichtig bei:

- HAME-/hm2mqtt-Sensoren mit langsamer Telemetrie.
- Restore-States.
- `unknown`/`unavailable`.
- Template-Sensoren, die fehlende Rohwerte über `float(0)` abfangen.
- Aggregaten mit Last-Value-Fallback.

### Gesamt-SOC-Sonderfall

`sensor.verbleibende_energie_bis_mindest_soc` kann mit zuletzt gültigen Einzel-SOCs weiterrechnen.

Für historische Analysen sollten deshalb zusätzlich exportiert werden, falls genutzt:

- `daten_vollstaendig_aktuell`.
- `quellen_mit_letztem_wert`.

Sonst sieht ein grafisch kontinuierlicher SOC-Verlauf fälschlich wie vollständig gemessene Live-Evidenz aus.

## 7. Zeitzone

Operative Darstellung: `Europe/Berlin`.

Für maschinelle Speicherung bevorzugt:

- UTC-Zeitstempel, oder
- Offset-aware ISO-8601.

Keine naive lokale Zeit ohne Offset, da Sommer-/Winterzeit sonst doppelte/fehlende Zeitpunkte erzeugen kann.

## 8. Retention / Datenmenge

Recorder hält laut Snapshot nur 30 Tage Detaildaten.

Folge:

- täglicher Export muss spätestens innerhalb dieses Fensters erfolgen, wenn Detailhistorie dauerhaft bewahrt werden soll.
- für robuste Pipeline ist täglicher Pull sinnvoll, aber nicht zwingend sekundengenau zeitkritisch.

1-Minuten-Rohdaten **aller** HA-Entities ungefiltert in Git sind nicht sinnvoll.

Bevorzugt:

- explizite Sensor-Whitelist.
- Klima 1-min-Auflösung.
- Energie-/Leistungsdaten nur für konkrete Analysefälle oder aggregiert.
- Rohdaten und abgeleitete Tages-/Wochenkennzahlen getrennt.

## 9. Privacy-Gate

Das Repository ist derzeit öffentlich.

Klima-, Verbrauchs-, Geräte- und Zeitreihendaten können Anwesenheit und Tagesabläufe offenlegen.

Darum ist automatischer Detaildatenexport aktuell:

**BLOCKED BY PRIVACY DECISION**.

Vor Aktivierung:

1. Repo-Sichtbarkeit bewusst entscheiden.
2. Sensor-Whitelist festlegen.
3. Retention in Git festlegen.
4. prüfen, ob 1-min-Daten wirklich für alle ausgewählten Sensoren nötig sind.
5. Secrets vollständig außerhalb des Repos halten.

## 10. Vorgeschlagene Sensorgruppen

### Klima – Zielgruppe A

- SwitchBot Temperatur.
- SwitchBot Feuchte.
- Govee Temperatur.
- Govee Feuchte.
- ggf. AC Infinity Temperatur/Feuchte.

Exakte Entity-IDs: `OPEN`.

### Energie – Zielgruppe B, optional

Für Regelanalyse wären besonders wertvoll:

- `sensor.shelly_3em_netto_leistung`.
- `sensor.shelly_3em_netto_leistung_30s_mittel`.
- Venus-D-Shelly-Power.
- Venus-D-SOC.
- SF2400 AC Input-/Output-Limit.
- SF2400 AC bidirektionale Shelly-Power.

Wegen Anwesenheits-/Verbrauchsprofilen nur nach Privacy-Entscheidung dauerhaft exportieren.

## 11. AC Infinity

Offener Punkt:

- konkrete vorhandene Geräte identifizieren.
- aktuelle HA-Integrationsmöglichkeit verifizieren.
- lokal vs. Cloud bewerten.
- Read-/Write-Fähigkeit und Updateintervall dokumentieren.

Erst danach in Sensor-Whitelist aufnehmen.

## 12. Pipeline-Acceptance

Jeder Lauf sollte prüfen:

- Zeitraum vollständig.
- erwartete Sensoren vorhanden.
- keine Dubletten.
- Einheiten konsistent.
- Zeitzone korrekt.
- `unknown`/`unavailable`/stale nicht still in 0 umgewandelt.
- Last-Value-Fallbacks gekennzeichnet.
- keine Secrets/private URLs/IPs enthalten.
- Datei erst nach vollständigem Lauf als erfolgreich publizieren.

## 13. Noch fehlende Informationen vor Implementierung

- aktuelle HA-Core-Version.
- Installationsart.
- Live-Bestätigung der Recorder-Konfiguration.
- genaue Klima-Entity-IDs.
- Repo-Privacy-Entscheidung.
- gewünschte Sensor-Whitelist.

Die frühere Lücke „welche Datenbank?“ ist durch den letzten Config-Snapshot **vorläufig geklärt: SQLite**.