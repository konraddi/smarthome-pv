# Runbook – Aktuellen Home-Assistant-Stand importieren

Ziel: das neue GitHub-Projekt aus dem **tatsächlich aktuellen** Home-Assistant-Zustand befüllen, ohne alte Chats als Konfigurationsquelle zu missbrauchen.

## 1. Systembasis

Erfassen:

- Home Assistant Core-Version
- Installationsart
- relevante Add-ons/Custom Components
- Recorder-/DB-Typ
- Backupstatus

## 2. Relevante Entities

Zuerst nur projektkritische Gruppen:

### Netz / Shelly

- Phasenleistungen
- Nettoleistung
- Netzbezug/-einspeisung Energie

### PV

- relevante WR-/PV-Leistungen
- Produktionszähler

### SolarFlow 2400 AC

- SOC
- Lade-/Entladeleistung
- Betriebsmodus
- Limits
- Availability

### SolarFlow 800 Plus

- SOC
- PV-/AC-Input
- Output
- Modus/Limits
- Availability

### Marstek Venus D

- SOC
- Leistung
- Status
- Freshness / last_changed / last_updated soweit nutzbar

### Smart Plugs

- Anzucht
- Outdoor Plug
- weitere energie-relevante Geräte

### Klima

- ausgewählte SwitchBot-/Govee-Entities

## 3. Automationen

Aktuell produktive YAML vollständig exportieren für:

- SolarFlow 2400 AC
- SolarFlow 800 Plus
- weitere Automationen, die dieselben Aktoren oder kritischen Sensoren verwenden

## 4. Abgleich

Für jede Automation:

- stimmen Entity-IDs mit aktuellem HA überein?
- stimmen Vorzeichen und Einheit?
- welche Inputs können `unknown`/`unavailable` werden?
- welche Werte werden mit `float(0)` oder anderem Default behandelt?
- gibt es stale/Restore-Risiko?
- wer schreibt dieselben Aktoren?

## 5. GitHub-Pflege

Transaktional aktualisieren:

- `CURRENT_SYSTEM_STATUS.md`
- `DEVICE_REGISTER.md`
- `ENTITY_REGISTER.md`
- `INTEGRATION_REGISTER.md`
- `AUTOMATION_REGISTER.md`
- `ENERGY_FLOW_MODEL.md`
- `AUTOMATIONS/*.yaml`
- `OPEN_ITEMS.md`
- `PROJECT_TIMELINE.md`

## 6. Sicherheitsfilter

Vor GitHub-Write entfernen/ersetzen:

- Tokens
- Passwörter
- externe Zugangsdaten
- private IPs, wenn nicht zwingend nötig
- sonstige Secrets

Da das Repository aktuell öffentlich ist, besonders restriktiv vorgehen.