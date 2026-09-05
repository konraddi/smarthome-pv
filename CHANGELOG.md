# CHANGELOG.md – Agent-Design

Dokumentiert **Methodik-, Governance-, Schema- und verbindliche Workflowänderungen des Agenten**, nicht normale Fortschreibung des realen Smart-Home-/PV-Zustands. Betriebsfakten bleiben in den zuständigen Status-, Register-, Timeline- und Datenmodell-Dateien.

## [0.2.0-beta] – 2026-09-05

### Added
- formale Agent-Versionierung mit `VERSION.md`
- verbindlicher `CHANGELOG.md` für Agent-/Methodikänderungen
- `RUNBOOKS/AGENT_VERSIONING.md` mit Versionsklassifikation, Snapshot-Pflicht, Release-Gates und Rollback-Verfahren
- read-only Snapshot-Branch vor materialem Methodikumbau
- klare Trennung zwischen Agent-Version und normaler Betriebs-/Inventarfortschreibung

### Changed
- `PROJECT_INSTRUCTIONS.md` verweist auf aktive Version, Changelog und Versionierungs-Runbook und macht Snapshot/Verifikation vor neuen Agent-Releases verbindlich
- `README.md` dokumentiert die neue Versionierungsstruktur

### Behavior impact
- Designänderungen werden künftig als PATCH/MINOR/MAJOR klassifiziert
- eine neue Agent-Version darf erst nach erfüllter Snapshot-Pflicht, aktualisierter Versions-/Changelog-Dokumentation und Write-Verifikation als aktiv bezeichnet werden
- normale Änderungen an Messwerten, Geräten, Entities, Integrationen, Automations-Ist-Zuständen, Open Items oder Timeline führen nicht automatisch zu einer neuen Agent-Version
- Methoden-Rollback darf neuere reale Betriebs-/Mess-/Registerdaten nicht still zurücksetzen

### Rollback
- vorheriger Stand: `0.1.0-beta`
- Snapshot: `snapshot/beta-0.1.0-2026-09-05-pre-versioning`
- Snapshot basiert auf `main`-Commit `780649d0acc2ebb43d9172dd5d2ca93806c60a4c`

## [0.1.0-beta] – Baseline bis 2026-09-05

Retrospektiv benannte erste zusammenhängende Agent-Design-Baseline. Der eingefrorene Zustand entspricht `main`-Commit `780649d0acc2ebb43d9172dd5d2ca93806c60a4c`.

Enthielt insbesondere:
- GitHub `konraddi/smarthome-pv` als alleinige kanonische, beschreibbare Source of Truth
- `PROJECT_INSTRUCTIONS.md` als höchste Projekt-Governance und `AGENTS.md` als vollständige Methodik
- striktes Evidenzmodell mit `MEASURED`, `OBSERVED`, `DOCUMENTED`, `REPORTED`, `INFERRED`, `HYPOTHESIS`, `UNKNOWN`
- Freshness-/Restore-/`unknown`-/`unavailable`-Regeln für steuerungsrelevante Daten
- Energie- und Messmodell mit Messpunkt, Einheit, Vorzeichen/Richtung, Updateintervall, Datenalter und HA-Semantik
- Grundsatz `ONE CONTROLLER PER ACTUATOR UNLESS COORDINATION IS VERIFIED`
- explizites Failsafe-Design und Verbot, letzten SOC oder kritische stale Werte still als aktuell weiterzuverwenden
- hypothesengesteuerte Fehleranalyse und Home-Assistant-YAML-/Template-Prüflogik
- Geräte-, Entity-, Integrations- und Automationsregister
- kanonisches `ENERGY_FLOW_MODEL.md`
- Acceptance-Tests, Datenpipeline, Operating Rhythm, Decisions, Open Items und Projekt-Timeline
- operative Runbooks für Automationsänderungen, Incidents/Failsafes, neue Geräte/Entities, HA-Updates, Datenexport und HA-Inventarimport
- Security-/Privacy-Regeln für das öffentliche Repository
- evidenzdatierte Vertiefung des realen Home-Assistant-/PV-Bestands mit aktueller vs. historischer Topologie, Integrationspfaden und bekannten Lücken

Diese Baseline wurde vor Einführung der formalen Versionierung unverändert als read-only Snapshot gesichert.
