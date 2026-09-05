# Runbook – Agent-Versionierung, Snapshots und Rollback

## Ziel

Relevante Änderungen am Smart-Home-/PV-Agent-Design reproduzierbar versionieren, ältere Methodikstände unverändert erhalten und einen sicheren Rollback ermöglichen, ohne neuere reale Anlagen-, Betriebs- oder Messdaten zu verlieren.

## 1. Was ist eine Agent-Design-Änderung?

Versionierungsrelevant sind insbesondere Änderungen an:
- `PROJECT_INSTRUCTIONS.md`
- `AGENTS.md`
- Wahrheits-/Evidenz-/Freshness-Modell
- Energiefluss-, Vorzeichen- oder Messsemantik als methodischem Vertrag
- verbindlichen Workflows, Gates und Failsafe-Regeln
- `RUNBOOKS/`
- Write-/Persistenz-/Rollback-Semantik
- grundlegender Home-Assistant-/PV-Regellogik als Agent-Methode
- Datenmodell-/Schemaänderungen an kanonischen Registern

Keine neue Agent-Version sind normalerweise:
- neue oder korrigierte Geräte-/Entity-/Integrationsfakten
- aktuelle Sensor-/SOC-/Leistungswerte
- Betriebsereignisse und Timeline-Fortschreibung
- neue offene Punkte oder geschlossene Gaps
- dokumentierte Änderungen am realen System, solange die Agent-Methode unverändert bleibt
- reine redaktionelle Korrekturen ohne methodische Wirkung

## 2. Versionsschema

Solange Beta: `MAJOR.MINOR.PATCH-beta`.

### PATCH
Begrenzte methodische Korrektur oder Präzisierung ohne neue Architektur oder inkompatible Semantik.

Beispiele:
- klarere Freshness-Formulierung
- kleiner Runbook-Fix
- Korrektur eines methodischen Widerspruchs

### MINOR
Neue relevante Fähigkeit oder wesentliche kompatible Methodik-/Workflowerweiterung.

Beispiele:
- neues verbindliches Runbook
- neue Qualitätssicherungsstufe
- neue Versionierungs-/Rollback-Architektur
- neuer systematischer Failsafe-/Verifikationsschritt
- neue kanonische Register- oder Datenmodellschicht

### MAJOR
Fundamentale oder inkompatible Änderung des Agent-, Steuerungs- oder Datenmodells bzw. bewusster Wechsel der Versionslinie.

## 3. Snapshot-Pflicht

Vor:
- jedem MINOR-Release
- jedem MAJOR-Release
- jedem materialem PATCH mit realistischem Rollback-Bedarf

muss **vor dem ersten Design-Write** ein Snapshot des aktuellen `main`-HEAD angelegt werden.

Namensschema:

`snapshot/beta-X.Y.Z-YYYY-MM-DD-pre-<slug>`

Beispiel:

`snapshot/beta-0.1.0-2026-09-05-pre-versioning`

Snapshot-Branches sind **read-only**: Nach Erstellung nicht fortschreiben oder für neue Arbeit verwenden.

Da das Repository öffentlich ist, dürfen Snapshot-Branches ebenfalls keine Secrets oder absichtlich nicht zu persistierenden Anwesenheits-/Klima-Rohdaten enthalten.

## 4. Release-Ablauf

1. aktuelle `VERSION.md`, `CHANGELOG.md`, `AGENTS.md`, `PROJECT_INSTRUCTIONS.md` und das aufgabenspezifische Runbook lesen
2. Änderung als PATCH/MINOR/MAJOR klassifizieren
3. Zielversion festlegen
4. aktuellen `main`-HEAD bestimmen
5. bei Snapshot-Pflicht Snapshot-Branch **vor** Änderungen anlegen
6. Snapshot-Existenz verifizieren
7. Agent-Design-Dateien ändern
8. `VERSION.md` aktualisieren
9. `CHANGELOG.md` ergänzen
10. bei geänderter `PROJECT_INSTRUCTIONS.md` vollständige neue Fassung im Chat ausgeben
11. alle Writes per Read-back oder Commit-Verifikation prüfen
12. erst dann neuen Agent-Stand als aktiv/kanonisch bezeichnen

## 5. Changelog-Regeln

`CHANGELOG.md` enthält nur relevante Agent-/Methodikänderungen.

Nicht hinein gehören als eigene Release-Gründe:
- neue aktuelle Sensorwerte
- normale Geräte-/Entity-/Integrationspflege
- gewöhnliche Betriebsereignisse
- reine Timeline-Fortschreibung
- normale Open-Item-Pflege

Jeder Release-Eintrag soll mindestens enthalten:
- Version und Datum
- wichtigste Änderungen
- Auswirkungen auf Verhalten/Workflow
- vorherige Version
- Rollback-Snapshot und dessen Baseline-Commit

## 6. Rollback – Standardfall

Ein Methoden-Rollback bedeutet **nicht** automatisch einen Git-Hard-Reset des gesamten Repositories.

Standard:
1. aktuellen Stand zuerst als `recovery/...` sichern, wenn ein echter Rückbau durchgeführt wird
2. Ziel-Snapshot öffnen
3. nur betroffene Methodik-/Governance-Dateien zurückführen, typischerweise:
   - `PROJECT_INSTRUCTIONS.md`
   - `AGENTS.md`
   - `README.md`
   - `VERSION.md`
   - `CHANGELOG.md` nur mit dokumentierter Rollback-Historie
   - betroffene `RUNBOOKS/`
4. **nicht** ungeprüft zurückrollen:
   - `CURRENT_SYSTEM_STATUS.md`
   - `SYSTEM_CONTEXT.md`, soweit seitdem reale Anlagenfakten ergänzt wurden
   - `DEVICE_REGISTER.md`
   - `ENTITY_REGISTER.md`
   - `INTEGRATION_REGISTER.md`
   - `AUTOMATION_REGISTER.md`
   - `PROJECT_TIMELINE.md`
   - `OPEN_ITEMS.md`
   - aktuelle Betriebs-/Messdaten oder importierte HA-Evidenz
5. wenn eine ältere Methodik ein anderes Schema erwartet, aktuelle Betriebsdaten gezielt kompatibel migrieren statt löschen
6. Rollback in `CHANGELOG.md` dokumentieren und aktive Version in `VERSION.md` ausweisen

## 7. Vollständiger Repository-Rollback

Nur im ausdrücklich begründeten Ausnahmefall.

Vorher zwingend:
- aktuellen `main` als Recovery-Branch sichern
- alle seit Snapshot entstandenen Anlagen-/Mess-/Register-/Timeline-Deltas inventarisieren
- festlegen, wie diese nach dem Rollback erhalten oder wieder eingespielt werden

Kein Force-Reset, der neuere reale Systeminformationen still vernichtet.

## 8. Release-Gates

Eine neue Version gilt erst als aktiv, wenn:
- Snapshot-Pflicht erfüllt und verifiziert
- `VERSION.md` korrekt
- `CHANGELOG.md` aktualisiert
- Methodikdateien konsistent
- `PROJECT_INSTRUCTIONS.md` bei Änderung vollständig im Chat ausgegeben
- alle Writes zurückgelesen oder der resultierende Commit verifiziert sind

## 9. Leitprinzip

> **SNAPSHOT BEFORE METHOD CHANGE. VERSION BEFORE CLAIM. CHANGELOG BEFORE FORGETTING. ROLLBACK METHOD, PRESERVE CURRENT SYSTEM TRUTH.**
