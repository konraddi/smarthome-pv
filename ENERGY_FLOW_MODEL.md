# Energy Flow Model

Stand: 2026-09-02

Diese Datei definiert das gemeinsame physikalische Modell für PV-, Netz- und Speicherautomationen. Konkrete Entity-IDs stehen in `ENTITY_REGISTER.md`.

## 1. Grundgrößen

- `P_grid` – Nettoleistung am Netzanschlusspunkt
- `P_pv` – gesamte aktuell wirksame PV-Erzeugung
- `P_load` – Hausverbrauch
- `P_bat_charge` – Ladeleistung eines Speichers
- `P_bat_discharge` – Entladeleistung eines Speichers

Das genaue Vorzeichen von `P_grid` wird erst nach Verifikation der kanonischen Shelly-Netto-Entity festgelegt.

## 2. Saldierende Betrachtung

Der Zähler ist saldierend. Deshalb muss eine netzflussbasierte Speicherregelung auf der Gesamtbilanz arbeiten.

Beispiel:

- Phase A importiert 500 W
- Phase B exportiert 800 W
- Phase C importiert 100 W

Netto ergibt sich ein Export von 200 W. Eine Regelung, die nur Phase A betrachtet, würde den realen Netzfluss falsch bewerten.

## 3. Kanonische Vorzeichenkonvention

Noch `VERIFY`.

Sobald verifiziert, wird projektweit genau eine Konvention festgelegt, bevorzugt z. B.:

- `P_grid > 0` = Netzbezug
- `P_grid < 0` = Netzeinspeisung

oder die tatsächlich vorliegende umgekehrte Konvention.

Automationen dürfen nicht jeweils eigene versteckte Vorzeichenlogik verwenden.

## 4. Rest-Einspeisungsziel

Mehrere bekannte Automationen zielen bewusst auf ungefähr **200 W Rest-Netzeinspeisung**, statt exakt 0 W anzustreben.

Zweck:

- Regelreserve gegen Mess-/Kommunikationslatenz
- geringeres Risiko eines kurzen Netzbezugs
- weniger Schwingen um den Nullpunkt

Der Wert von ungefähr 200 W ist eine aktuelle Projektleitlinie für bestimmte Automationen, keine universelle Grenze. Änderungen müssen pro Automation bewertet werden.

## 5. Speicherkoordination

Bekannte aktive Rollen:

- SolarFlow 2400 AC: primärer AC-gekoppelter Speicher / Überschussaufnahme und Nachtentladung
- SolarFlow 800 Plus: Zusatz-AC-Ladung und Nachtentladung
- Marstek Venus D: weiterer Speicher, dessen Zustand/Leistung in Zendure-Regelungen einfließen kann

Regelproblem:

Mehrere Speicher können denselben Überschuss „sehen“ und gleichzeitig reagieren. Deshalb ist eine definierte Priorisierung/Koordination erforderlich.

Projektprinzip:

> Nicht mehrere unabhängige Regler auf denselben Netzfehler loslassen.

## 6. Datenrate und Regeldynamik

Eine Regelung kann nicht sinnvoll schneller sein als ihre entscheidenden Eingangsdaten.

Beispiel Venus D:

- hm2mqtt/HAME-Telemetrie ungefähr 1 Minute
- damit ist der Pfad für schnelle Sekundenregelung ungeeignet
- ein Venus-D-Signal darf deshalb eher als langsamer Zustands-/Sperrinput verwendet werden, sofern seine Freshness ausreichend ist

Für schnelle Netzregelung ist der Shelly-Pfad typischerweise wichtiger; exakte Updatefrequenz ist noch zu inventarisieren.

## 7. Stale-/Unavailable-Modell

Für jeden kritischen Input gibt es drei logisch getrennte Fälle:

1. **VALID/FRESH** – darf regeln
2. **VALID BUT STALE** – historischer Wert, nicht ohne Weiteres als Steuerwert zulässig
3. **UNKNOWN/UNAVAILABLE** – Wert fehlt

Ein letzter SOC ist bei Fall 2 oder 3 nicht automatisch nutzbar.

## 8. Begrenzung und Clamping

Jede berechnete Lade-/Entladeleistung muss vor dem Aktor auf den erlaubten Bereich begrenzt werden:

`P_cmd = clamp(P_requested, P_min, P_max)`

Dabei sind Geräte-, Betriebs-, SOC-, Temperatur- und ggf. regulatorische Grenzen zu berücksichtigen. Werte nicht aus Modellwissen raten; die aktuell wirksamen Grenzen müssen verifiziert werden.

## 9. Mittelwerte

Ein 30-Sekunden-Mittelwert kann sinnvoll sein, wenn ein Regler auf einen stark schwankenden Shelly-Wert reagiert und dadurch unnötig schwingt. Er ist aber nicht automatisch für jede Automation richtig.

Bewertungskriterien:

- reale Sensor-Updatefrequenz
- gewünschte Reaktionszeit
- Trägheit des gesteuerten Speichers
- typische Lastsprünge
- Risiko von Netzbezug vs. Einspeiseüberschuss
- vorhandene Hysterese

Mittelwert, Hysterese und Mindesthaltezeit nicht blind kombinieren; sonst wird die Regelung zu träge.

## 10. Energiekonten

Leistung und Energie strikt trennen:

- Momentanwert: W
- Zähler: Wh/kWh

Ein früher als PV-Erzeugungsmesser genutzter Shelly kann später als Verbraucher eingesetzt werden. Die historische Nutzung ändert nicht die aktuelle Energieflussrichtung. Energy-Dashboard-Zuordnung muss zur aktuellen physikalischen Rolle passen.