# Entity Register

Stand: 2026-09-02

Diese Datei ist die kanonische Zuordnung wichtiger Home-Assistant-Entities. Noch nicht verifizierte IDs werden nicht erfunden.

## 1. Schema

| Entity-ID | Gerät/Quelle | Größe | Einheit | Richtung/Vorzeichen | Update/Freshness | Nutzung | Status |
|---|---|---|---|---|---|---|---|
| `UNKNOWN` | Shelly Pro 3EM | Netto-Haus-/Netzleistung | W | VERIFY | VERIFY | Speicherregelung | IMPORT REQUIRED |
| `UNKNOWN` | SolarFlow 2400 AC | SOC | % | n/a | VERIFY | Lade-/Entladefreigabe | IMPORT REQUIRED |
| `UNKNOWN` | SolarFlow 2400 AC | Input/Charge Power | W | VERIFY | VERIFY | Ladeleistung | IMPORT REQUIRED |
| `UNKNOWN` | SolarFlow 2400 AC | Output/Discharge Power | W | VERIFY | VERIFY | Entladeleistung | IMPORT REQUIRED |
| `UNKNOWN` | SolarFlow 800 Plus | SOC | % | n/a | VERIFY | Zusatzlade-/Nachtlogik | IMPORT REQUIRED |
| `UNKNOWN` | SolarFlow 800 Plus | Input Limit | W | n/a | VERIFY | Aktor/Status | IMPORT REQUIRED |
| `UNKNOWN` | SolarFlow 800 Plus | Output Limit | W | n/a | VERIFY | Aktor/Status | IMPORT REQUIRED |
| `UNKNOWN` | Marstek Venus D via hm2mqtt | SOC | % | n/a | ca. 1 min nominal | kritischer Failsafe-Input | IMPORT REQUIRED |
| `UNKNOWN` | Marstek Venus D / Shelly | Leistung | W | VERIFY | VERIFY | Erkennung tagsüber Entladung | IMPORT REQUIRED |
| `UNKNOWN` | Shelly Plug S Gen3 – Anzucht | Energie | kWh | Verbrauch | counter semantics VERIFY | Energy Dashboard consumed | IMPORT REQUIRED |
| `UNKNOWN` | Outdoor Plug | Energie | kWh | Verbrauch | counter semantics VERIFY | Energy Dashboard consumed | IMPORT REQUIRED |

## 2. Pflichtfelder für regelkritische Entities

Für jede Entity, die eine Automation steuert, mindestens dokumentieren:

- exakte Entity-ID
- physisches Gerät / Messpunkt
- Größe und Einheit
- Vorzeichen/Richtung
- erwartetes Updateintervall
- maximal akzeptiertes Datenalter für den konkreten Use Case
- Verhalten bei `unknown`/`unavailable`
- Restore-State möglich?
- Quelle/Integration
- abhängige Automationen

## 3. Energy-Dashboard-Semantik

Vor Zuordnung prüfen:

- `device_class`
- `state_class`
- Einheit
- monotone Zählersemantik bei `total_increasing`
- ob Reset/Wrap vorkommt
- physikalische Richtung

Bekannte Entscheidung:

- Anzucht-Shelly aktuell als **Verbrauch/consumed**.
- Outdoor Plug ebenfalls als **Verbrauch/consumed**.

Historische frühere Nutzung eines Steckers als Erzeugungsmessung ändert nicht die aktuelle physikalische Richtung.

## 4. Availability-Regel

Kritische Templates dürfen `unknown`/`unavailable` nicht mit einem semantisch gefährlichen Defaultwert kaschieren.

Beispiel problematisch:

`states('sensor.soc') | float(0)`

wenn `0` anschließend eine echte Regelentscheidung auslöst.

Stattdessen Availability/Freshness explizit prüfen und Failsafe auslösen.

## 5. Import

Die vollständige Entity-Liste soll aus dem aktuellen Home-Assistant-Stand importiert werden. Erst danach konkrete IDs ergänzen; keine Rekonstruktion aus alten Chats.