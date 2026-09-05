# VERSION.md – Agent-Version

## Aktiver Stand

- **Version:** `0.2.0-beta`
- **Status:** ACTIVE / BETA
- **Release-Datum:** 2026-09-05
- **Vorheriger Stand:** `0.1.0-beta`
- **Rollback-Snapshot:** `snapshot/beta-0.1.0-2026-09-05-pre-versioning`
- **Baseline-Commit:** `780649d0acc2ebb43d9172dd5d2ca93806c60a4c`

## Bedeutung

`0.2.0-beta` führt die formale Agent-Versionierung für Smart Home & PV ein. Methodik-, Governance-, Schema- und verbindliche Workflowänderungen werden künftig als Agent-Releases klassifiziert, vor materialem Umbau per read-only Snapshot gesichert und in `CHANGELOG.md` nachvollziehbar dokumentiert.

Normale Fortschreibung des realen Anlagenzustands – etwa neue Messwerte, aktuelle Geräte-/Entity-/Integrationsdaten, Betriebsereignisse, offene Punkte oder Timeline-Einträge – ist dagegen kein Versionsbump, solange die Agent-Methodik unverändert bleibt.

## Versionsschema

Format: `MAJOR.MINOR.PATCH-beta`, solange das Agent-Design im Beta-Stadium bleibt.

- **PATCH** – begrenzte methodische Korrektur/Präzisierung ohne neue Architektur oder inkompatible Semantik.
- **MINOR** – neue relevante Fähigkeit, Gate, Runbook oder wesentliche kompatible Methodik-/Workflowänderung.
- **MAJOR** – fundamentale oder inkompatible Architektur-/Datenmodelländerung bzw. bewusster Wechsel der Versionslinie.

Vor jedem MINOR-/MAJOR-Release und vor materialem PATCH mit realistischem Rollback-Bedarf ist vor dem ersten Design-Write ein Snapshot des aktuellen `main` anzulegen.

## Rollback-Grundsatz

Methoden-Rollback darf keine später hinzugekommenen Betriebs-, Mess-, Geräte-, Entity-, Integrations-, Automations-, Timeline- oder Statusdaten still vernichten. Standardmäßig werden nur die betroffenen Methodik-/Governance-Dateien zurückgeführt; falls ein älteres Schema nicht zu aktuellen Betriebsdaten passt, werden die Daten gezielt kompatibel migriert statt gelöscht.

Details: `RUNBOOKS/AGENT_VERSIONING.md` und `CHANGELOG.md`.
