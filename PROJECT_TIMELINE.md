# Project Timeline

Stand: 2026-09-02

Chronologie tatsächlich bekannter Projekt-Ereignisse. Keine vermuteten Ereignisse eintragen.

## 2026-09-02

- GitHub-Repository `konraddi/smarthome-pv` als kanonische Projektakte initialisiert.
- Agent-/Governance-Struktur angelegt.
- Bekannten PV-/Speicher-/Home-Assistant-Kontext initial inventarisiert.
- Projektgrenze zu `konraddi/Heise-Testberichte` festgelegt.
- Privacy-Risiko des derzeit öffentlichen Repositories als Open Item aufgenommen.
- Venus-D-SOC-Ausfallregel als verbindlicher Failsafe-Grundsatz übernommen.
- Klima-Datenpipeline als eigener Designbereich angelegt.

## 2026-09-01

- Ziel formuliert, Klima-/Feuchtewerte aus Home Assistant regelmäßig in das Projekt zu übernehmen.
- ungefähr 1-Minuten-Auflösung als ausreichend festgelegt.
- SwitchBot- und Govee-Daten als vorhandene HA-Quellen genannt.
- AC-Infinity-Integration bzw. Export als Prüfpunkt identifiziert.

## 2026-08-31

- Energiezuordnung des Anzucht-Shelly geprüft: aktuelle Nutzung ausschließlich Verbrauch; Zuordnung auf `consumed` ausgerichtet.
- Outdoor Plug in Verbrauchslogik aufgenommen.

## 2026-08 – Venus-D-/SolarFlow-Failsafe

- Venus-D-Status/SOC war nicht abrufbar bzw. der SOC über längere Zeit ausgefallen.
- Beobachtung: SolarFlow 2400 AC lud in dieser Situation nicht korrekt; Teile des Überschusses gingen ins Netz.
- Entscheidung: für die Regelung nicht den letzten bekannten Venus-D-SOC weiterverwenden, sondern einen expliziten Failsafe verwenden.

## 2026-07 – SolarFlow 800 Plus Automation

- Automation `Zendure SolarFlow 800 Plus AC-Zusatzladung und Nachtentladung` im Projekt iteriert.
- bekannte Logik: Zusatz-AC-Ladung tagsüber, Nachtentladung, Rest-Einspeisungsziel und Koordination mit Venus-D-Verhalten.

## 2026-06/07 – SolarFlow 2400 AC Automation

- Home-Assistant-Steuerung für Tages-/Nachtbetrieb und Überschussmanagement iteriert.
- Rest-Einspeisung statt harter Nullregelung als praktische Leitlinie genutzt.

## Historischer Bestand

- PV-Anlage mit 29 Modulen und ungefähr 8–8,5 kWp im Betrieb.
- Hoymiles HM-1500-4T, HMS-800W-2T, HMS-2000-4T und SMA-Stringwechselrichter im Projektkontext.
- Shelly Pro 3EM als zentraler Messpfad.
- mehrere Speicher-/Energiesysteme im Projektkontext, darunter Zendure, Marstek, Jackery und Bluetti.