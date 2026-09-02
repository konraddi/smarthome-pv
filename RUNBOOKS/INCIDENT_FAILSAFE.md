# Runbook – Energie-Incident / Failsafe

Für Fälle wie unerwartete Netzeinspeisung, Netzbezug trotz Überschuss, falsches Laden/Entladen oder fehlende Speichertelemetrie.

## 1. Beobachtung sichern

Dokumentieren:

- Zeitpunkt/Zeitraum
- erwartetes Verhalten
- tatsächliches Verhalten
- zentrale Netzleistung
- relevante Speicherleistungen/SOC
- Automation Trace/Logs
- Verfügbarkeit und Datenalter kritischer Inputs

## 2. Nicht sofort Parameter optimieren

Zuerst prüfen:

- Sensor `unknown`/`unavailable`?
- Restore-/stale Wert?
- Vorzeichen/Messrichtung korrekt?
- zweiter Regler aktiv?
- Automation lief überhaupt?
- Gerät hat Command übernommen?
- Leistungs-/SOC-/Temperaturgrenze aktiv?

## 3. Kritische Datenlücke

Wenn ein für die Regelung notwendiger Sensor fehlt:

- letzten Wert nicht automatisch als live behandeln
- vorgesehenen Failsafe anwenden
- keine aggressivere Regelung zur „Kompensation“ starten

Venus-D-Beispiel:

Fehlt der Live-SOC, darf der alte SOC nicht die 2400-AC-Regelung freigeben oder blockieren, als wäre er aktuell.

## 4. Ursache klassifizieren

- SENSOR / FRESHNESS
- ENTITY / SEMANTIK
- AUTOMATION / LOGIK
- RACE CONDITION / CONTROL OWNER
- INTEGRATION / MQTT / API
- DEVICE / LIMIT / DERATING
- TIME WINDOW
- UNKNOWN

## 5. Fix

Nur die belegte Ursache bzw. stärkste testbare Hypothese adressieren.

## 6. Verifikation

Passende Tests aus `ACCEPTANCE_TESTS.md`, insbesondere:

- AT-03 Freshness
- AT-05 Control Ownership
- AT-06 Command → Effect
- AT-08 Restart
- AT-09 Geräte-/MQTT-Ausfall
- AT-10 Last-/PV-Sprung
- AT-11 Multi-Speicher

## 7. Postmortem

Bei relevantem Incident dokumentieren:

- Ursache
- Auswirkung
- warum Detection/Failsafe nicht genügte
- Fix
- Regressionstest
- Prevention / neue Guardrail