# Runbook – Automation ändern

## Ziel

Eine produktive Home-Assistant-Automation kontrolliert ändern, ohne veraltete YAML, falsche Sensorsemantik oder ungetestete Failsafes einzubauen.

## Ablauf

1. Aktuelle Automation direkt aus Home Assistant bzw. der kanonischen Quelle lesen.
2. Abhängige Entities in `ENTITY_REGISTER.md` auflösen.
3. Messpunkt, Einheit, Vorzeichen und Freshness der kritischen Inputs prüfen.
4. Control Owner und konkurrierende Automationen/HEMS prüfen.
5. Problem/Hypothese und gewünschtes Verhalten klar formulieren.
6. Möglichst nur eine unabhängige Regeländerung vornehmen.
7. YAML/Template syntaktisch prüfen.
8. Failsafe-Pfade für `unknown`, `unavailable`, stale und Restart prüfen.
9. Änderung nach ausdrücklicher Freigabe auf das Live-System anwenden.
10. Passende `ACCEPTANCE_TESTS.md` ausführen.
11. Nicht nur Service-Write, sondern Gerätereaktion und physischen Netz-/Energieeffekt verifizieren.
12. Kanonische YAML, Register, Status und Timeline aktualisieren.

## Stop-Bedingungen

STOPP und neu analysieren, wenn:

- Entity-ID oder Vorzeichen nicht eindeutig ist
- aktueller YAML-Stand unbekannt ist
- kritischer Input stale/unavailable ist
- zweite Automation dieselbe Stellgröße schreibt
- Geräte-/Firmwareverhalten unerwartet ist
- Änderung außerhalb des freigegebenen Plans nötig wird

## Rollback

Vor Änderung muss klar sein, wie der zuletzt verifizierte Automationsstand wiederhergestellt wird. Ein GitHub-Commit allein beweist nicht, dass der Live-HA-Stand identisch war.