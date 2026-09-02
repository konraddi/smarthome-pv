# Runbook – Home-Assistant-Zeitreihen exportieren

## Gate 1 – Privacy

Vor einem automatischen Export prüfen:

- Repository privat/öffentlich?
- Sensoren enthalten Anwesenheits-/Verbrauchsprofile?
- Sensor-Whitelist freigegeben?

Solange das Repo öffentlich ist und keine bewusste Freigabe vorliegt: keine detaillierten Home-Zeitreihen automatisiert persistieren.

## Gate 2 – Quelle

Verifizieren:

- HA-Version/Installationsart
- Recorder-/DB-Typ
- gewünschter Exportweg
- tatsächliche Entity-IDs

## Export

Bevorzugt nur ausgewählte Sensoren und den benötigten Zeitraum exportieren.

Für Klima ist ungefähr 1-Minuten-Auflösung ausreichend.

Pflichtmetadaten:

- Zeitstempel mit Zeitzone/Offset
- Entity-ID
- Wert
- Einheit
- Qualitätsstatus

## Qualität

Nach jedem Lauf prüfen:

- Zeitraum vollständig
- erwartete Sensoren vorhanden
- keine still interpolierten Lücken
- `unknown`/`unavailable` erkennbar
- Einheiten plausibel
- keine Secrets enthalten

## Dokumentation

Bei produktivem Pipeline-Start:

- `DATA_PIPELINE.md`
- `CURRENT_SYSTEM_STATUS.md`
- `PROJECT_TIMELINE.md`
- ggf. `OPEN_ITEMS.md`

aktualisieren.