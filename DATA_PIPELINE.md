# Data Pipeline

Stand: 2026-09-02

Ziel: reproduzierbare Übernahme ausgewählter Home-Assistant-Zeitreihen in eine Form, die im Projekt analysiert und visualisiert werden kann.

## 1. Primärquelle

Home Assistant bzw. dessen Recorder-/Historien-Datenbank bleibt Primärquelle für Live- und Historienwerte.

GitHub-Daten sind exportierte Analysesnapshots, keine konkurrierende operative Datenbank.

## 2. Zielbild Klima

Gewünscht:

- täglicher Export
- etwa 1-Minuten-Auflösung ausreichend
- Temperatur und relative Luftfeuchte
- Quellen u. a. SwitchBot und Govee
- später ggf. AC Infinity
- Graph-/Trendbewertung im Projekt

## 3. Vorgeschlagenes Datenformat

Erst nach Privacy-Freigabe produktiv verwenden.

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
- `GAP`

Keine Lücken still interpolieren.

## 4. Zeitzone

Operative Darstellung: `Europe/Berlin`.

Für maschinelle Speicherung ist UTC bevorzugt, sofern der Exporter zuverlässig UTC liefert; lokale Darstellung dann mit expliziter Zeitzonenumrechnung. Alternativ lokale Zeit nur mit Offset speichern.

Nie naive Zeitstempel ohne Zeitzoneninformation verwenden, wenn Sommer-/Winterzeit relevant ist.

## 5. Retention / Git-Größe

1-Minuten-Rohdaten aller HA-Entities ungefiltert in Git zu schreiben ist nicht sinnvoll.

Bevorzugt:

- nur explizit ausgewählte Sensoren exportieren
- Rohdaten je Tag
- abgeleitete Tages-/Wochenaggregate getrennt erzeugen
- große oder hochfrequente Energiedaten nur bei konkretem Analysebedarf

Falls das Volumen steigt, später separaten Datenpfad oder objektbasierten Speicher prüfen, statt das Hauptrepo unbegrenzt aufzublähen.

## 6. Privacy-Gate

Das Repository ist derzeit öffentlich.

Klima-, Verbrauchs-, Bewegungs-, Geräte- und Zeitreihendaten können indirekt Anwesenheit und Tagesabläufe offenlegen.

Darum ist der automatische Detaildatenexport aktuell **BLOCKED BY PRIVACY DECISION**.

Vor Aktivierung:

1. Repository-Privatsphäre festlegen.
2. Sensor-Whitelist bestimmen.
3. retention festlegen.
4. Secrets aus Exportskripten ausschließen.

## 7. Exportwege – Entscheidung noch offen

Mögliche technische Pfade, erst nach Prüfung der vorhandenen HA-Umgebung auswählen:

- Home-Assistant-REST/WebSocket-API
- direkte Datenbankabfrage
- InfluxDB/Timeseries-Zwischenschicht
- Automation/Shell Command/Add-on
- externer Pull-Job

Keinen Pfad als gesetzt behandeln, bevor HA-Version, DB/Recorder und gewünschte Betriebsart verifiziert sind.

## 8. AC Infinity

Offener Punkt:

- prüfen, welche aktuelle HA-Integration für die vorhandenen AC-Infinity-Geräte geeignet ist
- alternativ prüfen, ob Daten über lokale/API-/Cloud-Schnittstelle exportierbar sind
- keine Integrationsfähigkeit aus Produktname ableiten

## 9. Qualitätsprüfung

Jeder Exportlauf sollte mindestens prüfen:

- erwarteter Zeitraum vollständig?
- erwartete Sensoren vorhanden?
- Einheiten konsistent?
- offensichtliche Dubletten?
- `unknown`/`unavailable` erhalten?
- Zeitzone korrekt?
- keine Secrets/unnötigen personenbezogenen Daten enthalten?