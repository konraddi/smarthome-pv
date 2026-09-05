# Smart Home & PV

Kanonische Projektakte für Home Assistant, Smart Home, PV-Energieflüsse, Batteriespeicher, Messkonzepte, Automationen und Integrationen.

## Ziel

Das Projekt soll nicht nur YAML sammeln, sondern den tatsächlichen Energie- und Systemzustand nachvollziehbar abbilden:

**OBSERVE → VERIFY → MODEL → CHANGE → TEST → COMPARE → DOCUMENT**

Schwerpunkte:

- Home Assistant als Orchestrierungs- und Beobachtungsebene
- PV-Erzeugung, Netzbezug/-einspeisung und saldierende Messung
- AC-/PV-gekoppelte Speicher und deren Koordination
- Zendure SolarFlow 2400 AC / SolarFlow 800 Plus
- Marstek-Systeme inkl. HAME Relay / hm2mqtt
- Shelly-Messung und Energy-Dashboard-Semantik
- sichere Home-Assistant-Automationen mit Failsafes
- Klima-/Sensordaten und spätere GitHub-Datenpipeline
- nachvollziehbare Geräte-, Entity-, Integrations- und Automationsregister

## Source of Truth

GitHub-Repository: `konraddi/smarthome-pv`

ChatGPT-Projektquellen sollen nicht als parallele Projektakte gepflegt werden. Die wirksame Kurz-Anweisung steht zusätzlich im ChatGPT-Feld **Projektanweisungen**; ihre versionierte Kopie liegt in `PROJECT_INSTRUCTIONS.md`.

## Agent-Version

Aktiver Stand und Rollback-Punkt stehen in `VERSION.md`. Agent-/Methodikänderungen werden in `CHANGELOG.md` versioniert; das Verfahren definiert `RUNBOOKS/AGENT_VERSIONING.md`.

Normale Änderungen am Anlagen-Ist-Zustand, an Geräte-/Entity-Registern, Messwerten oder Betriebsereignissen sind keine neue Agent-Version, solange Methodik, Schema oder verbindlicher Workflow unverändert bleiben.

## Wichtige Dateien

- `PROJECT_INSTRUCTIONS.md` – Governance und Orchestrierung
- `AGENTS.md` – vollständige Arbeitsmethodik
- `VERSION.md` – aktive Agent-Version und Rollback-Punkt
- `CHANGELOG.md` – Historie der Agent-/Methodikänderungen
- `SYSTEM_CONTEXT.md` – stabile Anlagen-/Systemarchitektur
- `CURRENT_SYSTEM_STATUS.md` – aktueller technischer Ist-Zustand
- `DEVICE_REGISTER.md` – physische Geräte
- `ENTITY_REGISTER.md` – kanonische HA-Entity-Zuordnung und Messsemantik
- `INTEGRATION_REGISTER.md` – Integrationen, Protokolle und Steuerpfade
- `AUTOMATION_REGISTER.md` – Automationsindex, Abhängigkeiten und Failsafes
- `ENERGY_FLOW_MODEL.md` – Leistungsrichtung, Energiefluss und Regelziele
- `ACCEPTANCE_TESTS.md` – Testkategorien für Änderungen
- `DATA_PIPELINE.md` – Klima-/Zeitreihenexport und Analysepfad
- `OPERATING_RHYTHM.md` – Wartungs-/Reviewlogik
- `DECISIONS.md` – dauerhafte Entscheidungen
- `OPEN_ITEMS.md` – ausschließlich offene Punkte
- `PROJECT_TIMELINE.md` – tatsächlich erfolgte Ereignisse
- `RUNBOOKS/` – operative Verfahren

## Vertraulichkeit

Das Repository ist beim Projektstart **öffentlich**. Deshalb werden hier keine Passwörter, Tokens, API-Keys, privaten Schlüssel, exakten externen Home-Assistant-Zugangsdaten, privaten IP-Adressen oder sonstigen Zugangsdaten abgelegt. Vor einem Export detaillierter Home-/Klima-Zeitreihen ist zu prüfen, ob das Repository privat betrieben werden soll, da solche Daten Anwesenheits- und Nutzungsprofile offenbaren können.

## Projektgrenze zu Heise-Testberichte

Das Repository `konraddi/Heise-Testberichte` bleibt Source of Truth für redaktionelle Produkttests, Review-Messreihen, Claims und Publikationsarbeit. `smarthome-pv` dokumentiert nur die für den realen Smart-Home-/Energiebetrieb relevanten Geräte-, Integrations-, Steuer- und Betriebsfakten. Review-Ergebnisse werden nicht doppelt gepflegt.
