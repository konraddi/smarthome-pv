# Runbook – Neues Gerät / neue Entity

## Gerät aufnehmen

1. Physisches Gerät eindeutig identifizieren.
2. stabile `DEV-*`-ID vergeben.
3. Rolle im Energie-/Smart-Home-System dokumentieren.
4. Integration/Protokoll bestimmen.
5. Read-/Write-Fähigkeit getrennt prüfen.
6. keine Seriennummer/Secrets unnötig ins Repo übernehmen.

## Entities aufnehmen

Je relevante Entity:

1. exakte Entity-ID aus aktuellem HA lesen.
2. Messpunkt bestimmen.
3. Größe und Einheit verifizieren.
4. Richtung/Vorzeichen verifizieren.
5. Updateintervall und reales Datenalter beobachten.
6. Restore-/Availability-Verhalten prüfen.
7. abhängige Automationen dokumentieren.
8. Energy-Dashboard-Rolle nur bei passender Semantik setzen.

## Aktor aufnehmen

Zusätzlich:

- bestätigter Wertebereich
- Command-Semantik
- Rücklesemöglichkeit
- Control Owner
- Konkurrenz durch Hersteller-App/HEMS
- Offline-Verhalten
- manueller Override

## Abschluss

Mindestens aktualisieren:

- `DEVICE_REGISTER.md`
- `ENTITY_REGISTER.md`
- `INTEGRATION_REGISTER.md`
- ggf. `AUTOMATION_REGISTER.md`
- `CURRENT_SYSTEM_STATUS.md`
- `PROJECT_TIMELINE.md`
- offene Lücken nach `OPEN_ITEMS.md`