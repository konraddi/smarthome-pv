# Acceptance Tests

Stand: 2026-09-02

Diese Datei definiert Testkategorien. Ein Test gilt nur als bestanden, wenn er tatsächlich ausgeführt und mit Evidenz dokumentiert wurde.

## AT-01 – YAML / Konfiguration

Prüfen:

- YAML syntaktisch gültig
- Templates rendern ohne Fehler
- referenzierte Entity-IDs existieren
- Services/Actions existieren
- Units/Datentypen passen

Syntax-Erfolg allein ist kein Funktionsnachweis.

## AT-02 – Messrichtung / Vorzeichen

Für jede regelkritische Leistungs-Entity:

- eindeutigen realen Zustand herstellen/erkennen
- prüfen, welches Vorzeichen Bezug/Einspeisung bedeutet
- HA-Anzeige gegen physikalisch erwartete Richtung vergleichen
- Ergebnis in `ENTITY_REGISTER.md` dokumentieren

## AT-03 – Freshness / Availability

Je kritischem Input testen:

- normal/fresh
- `unknown`
- `unavailable`
- stale/zu alt
- Restore-State nach HA-Neustart, soweit relevant

Erwartung: definierter Failsafe statt stiller Weiterregelung mit altem Wert.

## AT-04 – Grenzwerte / Clamping

Testen:

- unter Minimum
- innerhalb Bereich
- über Maximum
- negative/unerwartete Eingangswerte
- SOC-/Temperatur-/Gerätebegrenzung, soweit verfügbar

Kein Aktor darf außerhalb bestätigter Grenzen kommandiert werden.

## AT-05 – Control Ownership

Prüfen:

- ist HA der einzige aktive Regler der Stellgröße?
- greifen Hersteller-HEMS/App/Zeitplan gleichzeitig ein?
- gibt es andere Automationen, die dieselbe Entity schreiben?
- ist `mode`/Parallelität der Automation passend?

## AT-06 – Command → Effect

Nach Stellbefehl prüfen:

1. Command wurde gesendet
2. Gerätezustand/Limit wurde übernommen
3. reale Lade-/Entladeleistung reagiert
4. Netzfluss reagiert plausibel

Nur Schritt 1 ist kein bestandener Test.

## AT-07 – Zeitfenster

Testen:

- Startzeitpunkt
- Endzeitpunkt
- Übergangsfenster
- Mitternacht übergreifende Fenster
- Zeitzone Europe/Berlin
- Sommer-/Winterzeit, wenn relevant

## AT-08 – HA-Neustart / Reload

Prüfen:

- Automationszustand nach Restart
- Restore-Sensoren
- initiale `unknown`/`unavailable`-Phase
- ob ein Aktor unbeabsichtigt auf Default/alten Wert gesetzt wird
- Recovery nach Wiederverbindung

## AT-09 – Geräte-/MQTT-Ausfall

Prüfen:

- Speicher offline
- MQTT/Add-on/Relay nicht erreichbar
- verzögerte Updates
- Reconnect
- keine Endlosschleifen oder gefährliche Ersatzwerte

## AT-10 – Last-/PV-Sprung

Für netzflussbasierte Regler:

- plötzlicher Lastanstieg
- plötzlicher Lastabfall
- PV-Wolkenkante
- Speicher erreicht Limit/SOC-Grenze

Beobachten:

- Overshoot
- Schwingen
- Reaktionszeit
- Netzbezug
- Rest-Einspeisung

## AT-11 – Multi-Speicher-Koordination

Prüfen:

- 2400 AC lädt, 800 Plus wartet/ergänzt wie vorgesehen
- Venus D ändert Zustand
- kein gegenseitiges Aufschaukeln
- keine gleichzeitige unnötige Lade-/Entladegegenrichtung
- Failsafe bei fehlender Venus-D-Telemetrie

## AT-12 – Energy Dashboard

Für Energiezähler:

- physikalische Richtung stimmt
- `consumed`/`produced` korrekt
- keine doppelte Zählung
- State-Class passend
- Zähler steigt erwartungsgemäß
- Reset/Restore verhält sich plausibel

## AT-13 – Manueller Override

Wenn vorhanden:

- Override setzt Automation zuverlässig außer Kraft
- klarer Rückweg in Automatik
- keine konkurrierende Rücksetzung durch zweite Automation

## AT-14 – Regression

Nach relevanter HA-, Firmware-, Integrations- oder Automationsänderung die für den betroffenen Pfad relevanten Tests erneut durchführen. Nicht pauschal alle Tests als bestanden übernehmen.